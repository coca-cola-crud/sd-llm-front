<template>
  <div class="login" :style="'background-image:url('+ Background +');'">
    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      label-position="left"
      label-width="0px"
      class="login-form"
    >
      <h3 v-if="isLogin" class="title">
        登录
      </h3>
      <h3 v-else class="title">
        注册
      </h3>

      <!-- 登录和注册表单切换 -->
      <el-form-item prop="username">
        <el-input v-model="loginForm.username" type="text" auto-complete="off" placeholder="手机号">
          <svg-icon slot="prefix" icon-class="user" class="el-input__icon input-icon" />
        </el-input>
      </el-form-item>
      <el-form-item prop="password">
        <el-input
          v-model="loginForm.password"
          type="password"
          auto-complete="off"
          placeholder="密码"
          @keyup.enter.native="handleLogin"
        >
          <svg-icon slot="prefix" icon-class="password" class="el-input__icon input-icon" />
        </el-input>
      </el-form-item>
      <el-form-item v-if="!isLogin" prop="passwordConfirm">
        <el-input v-model="loginForm.passwordConfirm" type="password" auto-complete="off" placeholder="确认密码">
          <svg-icon slot="prefix" icon-class="password" class="el-input__icon input-icon" />
        </el-input>
      </el-form-item>
      <el-form-item v-if="isLogin" prop="code">
        <el-input
          v-model="loginForm.code"
          auto-complete="off"
          placeholder="验证码"
          style="width: 63%"
          @keyup.enter.native="handleLogin"
        >
          <svg-icon slot="prefix" icon-class="validCode" class="el-input__icon input-icon" />
        </el-input>
        <div class="login-code">
          <img :src="codeUrl" @click="getCode">
        </div>
      </el-form-item>
      <el-form-item v-if="!isLogin" prop="code">
        <el-input
          v-model="loginForm.msgCode"
          auto-complete="off"
          placeholder="短信验证码"
          style="width: 63%"
          @keyup.enter.native="handleLogin"
        >
          <svg-icon slot="prefix" icon-class="validCode" class="el-input__icon input-icon" />
        </el-input>
        <div class="login-code">
          <el-button :disabled="isCounting" @click="sendSms">{{ countdown || '发送短信' }}</el-button>
        </div>
      </el-form-item>
      <!-- 其他代码 -->
      <div v-if="isLogin" class="form-footer">
        <a class="link-type" @click="toggleForm(false)">没有账号,去注册</a>
      </div>

      <div v-else class="form-footer">
        <a class="link-type" @click="toggleForm(true)">已有账号，去登录</a>
      </div>

      <el-form-item>
        <el-button
          v-if="isLogin"
          :loading="loading"
          size="medium"
          type="primary"
          style="width:100%;"
          @click.native.prevent="handleLogin"
        >
          <span v-if="!loading">登 录</span>
          <span v-else>登 录 中...</span>
        </el-button>
        <el-button
          v-if="!isLogin"
          :loading="loading"
          size="medium"
          type="primary"
          style="width:100%;"
          @click.native.prevent="handleRegister"
        >
          <span v-if="!loading">注 册</span>
          <span v-else>注 册 中...</span>
        </el-button>
      </el-form-item>

    </el-form>

    <!--  底部  -->
    <div v-if="$store.state.settings.showFooter" id="el-login-footer">
      <span v-html="$store.state.settings.footerTxt" />
      <span v-if="$store.state.settings.caseNumber"> ⋅ </span>
      <a href="https://beian.miit.gov.cn/#/Integrated/index" target="_blank">{{ $store.state.settings.caseNumber }}</a>
    </div>
  </div>
</template>

<script>
import { encrypt } from '@/utils/rsaEncrypt'
import Config from '@/settings'
import { getCodeImg, register } from '@/api/login'
import Cookies from 'js-cookie'
import qs from 'qs'
import Background from '@/assets/images/background.webp'

export default {
  name: 'Login',
  data() {
    return {
      isCounting: false,
      countdown: null,
      countTimer: null, // 用于清理定时器
      isLogin: true,
      Background: Background,
      codeUrl: '',
      cookiePass: '',
      loginForm: {
        username: '',
        password: '',
        passwordConfirm: '',
        rememberMe: false,
        code: '',
        uuid: ''
      },
      loginRules: {
        username: [{ required: true, message: '手机号不能为空', trigger: 'blur' },
          {
            pattern: /^1[34578]\d{9}$/,
            message: '请输入有效的手机号码',
            trigger: 'blur'
          }],
        password: [{ required: true, trigger: 'blur', message: '密码不能为空' }],
        code: [{ required: true, trigger: 'change', message: '验证码不能为空' }],
        passwordConfirm: [{ required: true, trigger: 'blur', message: '确认密码不能为空' }]
      },
      loading: false,
      redirect: undefined
    }
  },
  watch: {
    $route: {
      handler: function(route) {
        const data = route.query
        if (data && data.redirect) {
          this.redirect = data.redirect
          delete data.redirect
          if (JSON.stringify(data) !== '{}') {
            this.redirect = this.redirect + '&' + qs.stringify(data, { indices: false })
          }
        }
      },
      immediate: true
    }
  },
  created() {
    // 获取验证码
    this.getCode()
    // 获取用户名密码等Cookie
    this.getCookie()
    // token 过期提示
    this.point()
  },
  beforeDestroy() {
    // 清理倒计时定时器
    if (this.countTimer) {
      clearInterval(this.countTimer)
    }
  },
  methods: {
    sendSms() {
      // 发送短信的逻辑
      // 验证手机号格式是否正确

      // 开始倒计时
      this.isCounting = true
      this.countdown = 60 // 倒计时60s

      this.countTimer = setInterval(() => {
        if (this.countdown && this.countdown > 0) {
          this.countdown--
        } else {
          this.clearCountdown()
        }
      }, 1000)
    },
    clearCountdown() {
      this.isCounting = false
      this.countdown = null
      if (this.countTimer) {
        clearInterval(this.countTimer)
        this.countTimer = null
      }
    },
    async register(params) {
      const res = await register(params)
      if (res.code === 200) {
        this.$message.success('注册成功')
        this.toggleForm(true)
      } else {
        this.$message.error(res.msg)
      }
    },
    handleRegister() { // 注册
      this.$refs.loginForm.validate(valid => {
        const params = {
          phone: this.loginForm.username,
          password: this.loginForm.password,
          code: this.loginForm.code,
          msgCode: this.loginForm.code,
          uuid: this.loginForm.uuid
        }
        if (valid) {
          this.loading = true
          // 调用注册api
          this.register(params)
        }
      })
    },
    toggleForm(isLogin) {
      this.isLogin = isLogin
    },
    getCode() {
      getCodeImg().then(res => {
        this.codeUrl = res.img
        this.loginForm.uuid = res.uuid
      })
    },
    getCookie() {
      const username = Cookies.get('username')
      let password = Cookies.get('password')
      const rememberMe = Cookies.get('rememberMe')
      // 保存cookie里面的加密后的密码
      this.cookiePass = password === undefined ? '' : password
      password = password === undefined ? this.loginForm.password : password
      this.loginForm = {
        username: username === undefined ? this.loginForm.username : username,
        password: password,
        rememberMe: rememberMe === undefined ? false : Boolean(rememberMe),
        code: ''
      }
    },
    handleLogin() {
      this.$refs.loginForm.validate(valid => {
        const user = {
          username: this.loginForm.username,
          password: this.loginForm.password,
          rememberMe: this.loginForm.rememberMe,
          code: this.loginForm.code,
          uuid: this.loginForm.uuid
        }
        if (user.password !== this.cookiePass) {
          user.password = encrypt(user.password)
        }
        if (valid) {
          this.loading = true
          if (user.rememberMe) {
            Cookies.set('username', user.username, { expires: Config.passCookieExpires })
            Cookies.set('password', user.password, { expires: Config.passCookieExpires })
            Cookies.set('rememberMe', user.rememberMe, { expires: Config.passCookieExpires })
          } else {
            Cookies.remove('username')
            Cookies.remove('password')
            Cookies.remove('rememberMe')
          }
          this.$store.dispatch('Login', user).then(() => {
            this.loading = false
            this.$router.push({ path: this.redirect || '/' })
          }).catch(() => {
            this.loading = false
            this.getCode()
          })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    point() {
      const point = Cookies.get('point') !== undefined
      if (point) {
        this.$notify({
          title: '提示',
          message: '当前登录状态已过期，请重新登录！',
          type: 'warning',
          duration: 5000
        })
        Cookies.remove('point')
      }
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss">
.login {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  background-size: cover;
}

.title {
  margin: 0 auto 30px auto;
  text-align: center;
  color: #707070;
}

.login-form {
  border-radius: 6px;
  background: #ffffff;
  width: 385px;
  padding: 25px 25px 5px 25px;

  .el-input {
    height: 38px;

    input {
      height: 38px;
    }
  }

  .input-icon {
    height: 39px;
    width: 14px;
    margin-left: 2px;
  }
}

.login-tip {
  font-size: 13px;
  text-align: center;
  color: #bfbfbf;
}

.login-code {
  width: 33%;
  display: inline-block;
  height: 38px;
  float: right;

  img {
    cursor: pointer;
    vertical-align: middle
  }
}

/* ... */
.form-footer {

  text-align: right;
  margin-bottom: 5px;
}

/* ... */
.link-type {
  font-size: 14px;
}
</style>
