<template>
  <div>
    <el-row>
      <el-form ref="elForm" :model="formData" :rules="rules" size="medium" label-width="100px">
        <el-col :span="16">
          <el-form-item label="草拟题目" prop="title">
            <el-input v-model="formData.title" placeholder="请输入草拟题目" clearable :style="{width: '100%'}" />
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="">
            <el-button type="primary" icon="el-icon-s-opportunity" size="small" />
          </el-form-item>
        </el-col>
        <el-col :span="16">
          <el-form-item label="关键词" prop="keywords">
            <el-input v-model="formData.keywords" placeholder="请输入关键词" clearable :style="{width: '100%'}" />
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="">
            <el-button type="primary" icon="el-icon-s-opportunity" size="small" />
          </el-form-item>
        </el-col>
        <el-col :span="24">
          <el-form-item label="文章内容" prop="content">
            <el-input
              v-model="formData.content"
              type="textarea"
              placeholder="请输入文章内容"
              :autosize="{minRows: 4, maxRows: 4}"
              :style="{width: '100%'}"
            />
          </el-form-item>
        </el-col>
        <el-col :span="16">
          <el-form-item label="相关文献" prop="references">
            <el-input v-model="formData.references" placeholder="请输入相关文献" clearable :style="{width: '100%'}" />
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="" prop="field119">
            <el-button type="primary" icon="el-icon-s-opportunity" size="small" />
          </el-form-item>
        </el-col>
        <el-col :span="24">
          <el-form-item label="文字描述" prop="description">
            <el-input
              v-model="formData.description"
              type="textarea"
              placeholder="请输入文字描述"
              :autosize="{minRows: 4, maxRows: 4}"
              :style="{width: '100%'}"
            />
          </el-form-item>
        </el-col>
        <el-col :span="24">
          <el-form-item size="large">
            <el-button type="primary" size="small" @click="submitForm">生成</el-button>
            <el-button size="small" @click="resetForm">重置</el-button>
          </el-form-item>
        </el-col>
      </el-form>
    </el-row>
    <el-row>
      <el-card>
        <el-row>
          <el-col :span="16">
            <el-input v-model="finaltitle" clearable :style="{width: '100%'}" />
          </el-col>
          <el-col :span="8">
            <el-button type="success" style="margin-left: 16px" @click="sendMsg">
              就它了
            </el-button>
            <el-button type="warning" style="float: right" @click="drawer = true">
              生成记录
            </el-button>
          </el-col>
        </el-row>
      </el-card>

      <el-drawer
        title="生成记录"
        :visible.sync="drawer"
        :direction="direction"
        :before-close="handleClose"
      >
        <span>我来啦!</span>
      </el-drawer>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'Topic',
  components: {},
  props: [],
  data() {
    return {
      finaltitle: '',
      drawer: false,
      direction: 'rtl',
      formData: {
        title: undefined,
        field114: undefined,
        keywords: undefined,
        field116: undefined,
        content: undefined,
        references: undefined,
        field119: undefined,
        description: undefined
      },
      rules: {
        title: [{
          required: true,
          message: '请输入草拟题目',
          trigger: 'blur'
        }],
        keywords: [{
          required: true,
          message: '请输入关键词',
          trigger: 'blur'
        }],
        content: [],
        references: [{
          required: true,
          message: '请输入相关文献',
          trigger: 'blur'
        }],
        description: [{
          required: true,
          message: '请输入文字描述',
          trigger: 'blur'
        }]
      }
    }
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {},
  methods: {
    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done()
        })
        .catch(_ => {})
    },
    submitForm() {
      this.$refs['elForm'].validate(valid => {
        if (!valid) return
        // TODO 提交表单
      })
    },
    resetForm() {
      this.$refs['elForm'].resetFields()
    },
    sendMsg() {
      this.$emit('descriptionUpdate', this.finaltitle)
    }
  }
}

</script>

<style scoped>

</style>
