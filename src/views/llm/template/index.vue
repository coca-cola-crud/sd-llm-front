<template>
  <iframe :src="url" title="W3School 在线教程" class="web" />
</template>

<script>
export default {
  name: 'LlmTemplate',
  data() {
    return {
      url: ''
    }
  },
  created() {
    console.log(this.$route.query)
    const url = this.$route.query.url
    this.url = url
  }
}

</script>

<style scoped>
.web{
  width: 100%;
  height: 80vh;
  border: none;
}

</style>
