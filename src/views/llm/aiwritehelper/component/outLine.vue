<template>
  <div>
    <el-row>
      <el-col :span="12">
        <el-form ref="elForm" :model="formData" :rules="rules" size="medium" label-width="100px">
          <el-col>
            <el-form-item label="模版" prop="template">
              <el-radio-group v-model="formData.template" size="medium">
                <el-radio
                  v-for="(item, index) in templateOptions"
                  :key="index"
                  :label="item.value"
                  :disabled="item.disabled"
                >{{ item.label }}</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
          <el-col>
            <el-form-item label="修改模版" prop="outlineTemplate">
              <el-input
                v-model="formData.outlineTemplate"
                type="textarea"
                placeholder=""
                :autosize="{minRows: 4, maxRows: 4}"
                :style="{width: '100%'}"
              />
            </el-form-item>
          </el-col>
          <el-col>
            <el-form-item label="提纲草稿" prop="draft">
              <el-input v-model="formData.draft" placeholder="请输入提纲草稿" clearable :style="{width: '100%'}" />
            </el-form-item>
          </el-col>
          <el-col>
            <el-form-item label="文章长度" prop="contentLength">
              <el-input v-model="formData.contentLength" placeholder="文章长度" clearable :style="{width: '100%'}" />
            </el-form-item>
          </el-col>
          <el-col>
            <el-form-item size="large">
              <el-button type="primary" size="small" @click="submitForm">生成</el-button>
              <el-button size="small" @click="resetForm">重置</el-button>
            </el-form-item>
          </el-col>
        </el-form>
      </el-col>
      <el-col :span="12">
        <el-card class="box-card" />
      </el-col>

    </el-row>

  </div>
</template>

<script>
export default {
  name: 'OutLine',
  components: {},
  props: [],
  data() {
    return {
      formData: {
        outlineTemplate: undefined,
        template: 1,
        draft: undefined,
        contentLength: undefined
      },
      rules: {
        outlineTemplate: [{
          required: true,
          message: '模版不能为空',
          trigger: 'blur'
        }],
        template: [{
          required: true,
          message: '模版不能为空',
          trigger: 'change'
        }],
        draft: [{
          required: true,
          message: '请输入提纲草稿',
          trigger: 'blur'
        }],
        field105: [{
          required: true,
          message: '文章长度',
          trigger: 'blur'
        }, {
          pattern: /[^\d]/g,
          message: '请输入数字',
          trigger: 'blur'
        }]
      },
      templateOptions: [{
        'label': '选项一',
        'value': 1
      }, {
        'label': '选项二',
        'value': 2
      }]
    }
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {},
  methods: {
    submitForm() {
      this.$refs['elForm'].validate(valid => {
        if (!valid) return
        // TODO 提交表单
      })
    },
    resetForm() {
      this.$refs['elForm'].resetFields()
    }
  }
}
</script>

<style scoped>

</style>
