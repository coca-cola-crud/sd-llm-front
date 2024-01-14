<template>
  <div>
    <!-- 步骤条组件 -->
    <el-card style="margin: 10px;">
      <el-steps finish-status="process">
        <el-step
          v-for="(item, index) in steps"
          :key="index"
          :title="item.title"
          :description="item.detail"
          :status="stepStatus[item.id]"
          @click.native="handleClick(item.id)"
        />
      </el-steps>
      <!--      <el-button type="primary">下一步<i class="el-icon-arrow-right el-icon&#45;&#45;right" /></el-button>-->
    </el-card>

    <!-- 子组件（例如，步骤的具体内容） -->
    <div style="max-height: calc(100vh - 72px); overflow: auto; margin: 20px;">
      <!-- 在子组件上监听自定义事件（这里是“descriptionUpdate”） -->
      <topic v-if="activeStep === '选题'" @descriptionUpdate="updateDescription" />
      <article-type v-if="activeStep === '文章类型'" @descriptionUpdate="updateDescription" />
      <out-line v-if="activeStep === '提纲生成'" @descriptionUpdate="updateDescription" />
    </div>
  </div>
</template>

<script>
import Topic from './component/topic'
import ArticleType from './component/articleType'
import OutLine from './component/outLine'
export default {
  name: 'AiWriteHelper',
  components: { OutLine, ArticleType, Topic },
  data() {
    return {
      activeStep: '文章类型',
      steps: [
        { id: '1', title: '文章类型', detail: '' },
        { id: '2', title: '选题', detail: '' },
        { id: '3', title: '提纲生成', detail: '' },
        { id: '4', title: '内容生成', detail: '' },
        { id: '5', title: '摘要生成', detail: '' },
        { id: '6', title: '全文下载', detail: '' }
      ],
      stepStatus: {
        '1': 'finish',
        '2': 'wait',
        '3': 'wait',
        '4': 'wait',
        '5': 'wait',
        '6': 'wait'
      }
    }
  },

  methods: {
    handleClick(step) {
      // reset all status to 'wait'
      Object.keys(this.stepStatus).forEach(key => {
        this.stepStatus[key] = 'wait'
      })

      // update current step to 'process'
      this.stepStatus[step] = 'finish'

      // update activeStep display name
      this.activeStep = this.steps.find(item => item.id === step).title
    },

    updateDescription(newDescription) {
      const activeStepItem = this.steps.find(item => item.title === this.activeStep)
      activeStepItem.detail = newDescription
    }
  }
}
</script>

