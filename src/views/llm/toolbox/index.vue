<template>
  <div>
    <el-col v-for="(card, index) in cardList" :key="index" :span="6" class="card-item">
      <el-card :style="{ 'transform': hoverCardIndex === index ? 'scale(1.1)' : 'none' }" @click.native="goDetail(card.url)" @mouseover.native="hoverCard(index)" @mouseleave.native="hoverCard(-1)">
        <div slot="header" class="clearfix">
          <span>{{ card.title }}</span>
        </div>
        <div class="card-icon">
          <i :class="card.icon" />
        </div>
        <div class="card-content">
          <p>{{ card.summary }}</p>
        </div>
      </el-card>
    </el-col>

  </div>
</template>

<script>
export default {
  data() {
    return {
      cardList: [
        {
          id: 1,
          title: 'Card 1',
          icon: 'el-icon-user',
          summary: 'This is card 1 summary',
          url: 'https://chat18.aichatos.xyz/#/chat/1698815376825'
        },
        {
          id: 2,
          title: 'Card 2',
          icon: 'el-icon-goods',
          summary: 'This is card 2 summary'
        },
        {
          id: 3,
          title: 'Card 3',
          icon: 'el-icon-message',
          summary: 'This is card 3 summary'
        },
        {
          id: 1,
          title: 'Card 1',
          icon: 'el-icon-user',
          summary: 'This is card 1 summary'
        },
        {
          id: 2,
          title: 'Card 2',
          icon: 'el-icon-goods',
          summary: 'This is card 2 summary'
        },
        {
          id: 3,
          title: 'Card 3',
          icon: 'el-icon-message',
          summary: 'This is card 3 summary'
        }
      ],
      hoverCardIndex: -1
    }
  },
  methods: {
    hoverCard(index) {
      this.hoverCardIndex = index
    },
    goDetail(url) {
      // 跳转到template
      //
      this.$router.push({ path: '/tool/index', query: { url: url }})
    }
  }
}
</script>

<style scoped>
.card-item {
  width: 300px;
  margin: 20px;
  transition: all 0.3s;
  cursor: pointer;
}

.card-icon {
  font-size: 50px;
  color: #409EFF;
  text-align: center;
  margin-top: 30px;
}

.card-content {
  text-align: center;
  margin-top: 20px;
  font-size: 14px;
  color: #666666;
}
</style>
