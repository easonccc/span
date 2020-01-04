<template>
  <div>
    <HeaderFrame :backIsDisplay="true"></HeaderFrame>
    <div id="main">
      <h4>发表评论</h4>
      <hr />
      <!-- 评论框 -->
      <van-cell-group>
        <van-field
          class="textarea"
          rows="4"
          :value="comments"
          autosize
          input-class="textarea"
          type="textarea"
          placeholder="请输入留言"
        />
      </van-cell-group>
      <!-- 评论提交按钮 -->
      <van-button class="btn_commit" type="info" @click="commit"
        >发表评论</van-button
      >
      <!-- 评论列表 -->
      <div class="comBox">
        <van-list
          v-model="loading"
          :finished="finished"
          finished-text="没有更多了"
          @load="onload"
        >
          <van-row v-for="(item, i) in comentList" :key="i">
            <div class="littleHead">
              <span>第{{ i + 1 }}楼</span>
              <span>用户： {{ item.user_name }}</span>
              <span>发表时间： {{ item.add_time }}</span>
            </div>
            <div class="conetentCom">{{ item.content }}</div>
          </van-row>
        </van-list>
      </div>
      <!-- 加载更多 -->
      <van-button color="#ff4444" plain @click="showMore">加载更多</van-button>
    </div>
    <!-- 底部区域 -->
    <FooterFrame homeLight="#1989fa"></FooterFrame>
  </div>
</template>
<script>
export default {
  data() {
    return {
      comments: '',
      comentList: [],
      // 定义传入的页码数
      pagenum: 1,
      loading: false,
      finished: false
    }
  },
  created() {
    this.getCommens()
  },
  methods: {
    // 获取评论数据
    async getCommens() {
      // 向服器发送请求
      const { data: res } = await this.$http.get(
        `/api/getcomments/${this.$route.params.id}?pageindex=${this.pagenum}`
      )
      console.log(res)
      this.comentList = res.message
      console.log(this.comentList)
      if (res.message.length === 0) {
        this.$toast('没有更多留言了！')
      }
    },
    //   点击发表评论触发
    commit() {
      if (this.comments === '') {
        this.$toast('留言不能为空')
      }
    },
    onload() {
      // 异步更新数据
      setTimeout(() => {
        for (let i = 1; i <= 5; i++) {
          this.comentList.push(this.comentList.length + 1)
        }
        // 加载状态结束
        this.loading = false
        // 数据全部加载完成
        if (this.comentList.length === 0) {
          this.finished = true
        }
      }, 500)
    },
    // 点击加载更多按钮获取评论数据
    showMore() {}
  }
}
</script>
<style lang="less" scoped>
#main {
  position: relative;
  bottom: 60px;
  margin-top: 120px;
  padding-left: 5px !important;
  padding-right: 5px !important;
}
h4 {
  text-align: left;
}
[class*='van-hairline']::after {
  border: 1px solid #d1d2d2;
}
btn_commit,
.van-button--normal {
  width: 100%;
  height: 44px;
  margin-top: 5px;
  box-sizing: border-box;
}
.textarea {
  height: 100px;
}
.comBox {
  margin-top: 3px;
  text-align: left;
  font-size: 12px;
  line-height: 32px;
}
.littleHead {
  background-color: #ccc;
}
.conetentCom {
  padding: 5px 0;
}
</style>
