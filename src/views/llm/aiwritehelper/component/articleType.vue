<template>
  <el-row type="flex" justify="center" align="middle">
    <el-col :span="8">
      <div style="text-align: center;margin-bottom: 30px;margin-top: 30%">
        <h2 style="color: #2c3e50">请先选择你的文章类型</h2>
      </div>
      <div style="text-align: center;">

        <el-button v-for="(buttonText, index) in buttonList" :key="index" @click="sendMsg(buttonText)">
          {{ buttonText }}
        </el-button>
      </div>
    </el-col>
  </el-row>
</template>

<script>
export default {
  data() {
    return {
      buttonList: ['课程作业', '小论文', '大论文', '年终总结', '日报']
    }
  },
  methods: {
    sendMsg(val) {
      this.$emit('descriptionUpdate', val)
    }
  }

}
</script>
