<template>
  <div class="news-list">
    <!-- 
      对应的参数介绍：
      allDataLista: 已获取到的所有数据集
      blockHeight： 当前列栏目的定高
      @bottom ：当前子组件在下拉到底部的时候，触发数据请求相关事件
      v-slot：作用域插槽，也叫带数据作用域的插槽，可以拿到子组件注入的对应数据进行响应
    -->
    <virtual-block
      class="scroller"
      :allDataList="this.listData"
      :blockHeight="100"
      @bottom="atBottom"
      v-slot="slotProp"
    >
      <div class="warpper" :style="slotProp.paddingStyle" ref="wapperBox">
        <div v-for="(item,index) in slotProp.needReanderList" :key="index">
          <!-- 显示一条新闻 -->
          <div class="one-new">
            <!-- 新闻左侧标题、评论、来源部分 -->
            <div class="new-left">
              <h3>{{ item.title }}</h3>
              <div>
                <p>
                  <img src="../assets/icons/msg.png" alt="评" />
                  <span>{{ item.reads }}</span>
                  <span>{{ item.from }}</span>
                </p>
                <h4>{{ item.date }}</h4>
              </div>
            </div>
            <!-- 新闻右侧图片部分 -->
            <div class="new-right">
              <img :src="imgsList[item.image]" alt="PIC" />
            </div>
          </div>
        </div>
      </div>
      <div class="loading" v-if="ifRequest">
        <div>{{msg}}</div>
      </div>
    </virtual-block>
  </div>
</template>

<script>
import img0 from "../assets/news/0.webp";
import img1 from "../assets/news/1.webp";
import img2 from "../assets/news/2.webp";
import img3 from "../assets/news/3.webp";
import img4 from "../assets/news/4.webp";
import img5 from "../assets/news/5.webp";
import img6 from "../assets/news/6.webp";
import img7 from "../assets/news/7.webp";
import img8 from "../assets/news/8.webp";
import img9 from "../assets/news/9.webp";
import img10 from "../assets/news/10.webp";
import img11 from "../assets/news/11.webp";
import img12 from "../assets/news/12.webp";
import img13 from "../assets/news/13.webp";
import img14 from "../assets/news/14.webp";
import img15 from "../assets/news/15.webp";
export default {
  data() {
    return {
      //用来存放当前数据源的对象数组
      listData: [],
      //用来通知子组件内部的加载提示是否需要显示
      ifRequest: true,
      //用来进行相应提示的通知
      msg: "小二正在努力，请耐心等待...",
      // 图片数组
      imgsList: [
        img0,
        img1,
        img2,
        img3,
        img4,
        img5,
        img6,
        img7,
        img8,
        img9,
        img10,
        img11,
        img12,
        img13,
        img14,
        img15,
      ],
    };
  },
  async mounted() {
    // 分批发送请求时，先请求一部分数据保证数据显示
    let request = await this.getMock(50);
    if (request && request.length > 0) {
      this.listData = [...request];
    }
  },
  methods: {
    // 发送请求获取新的请求模拟数据，这个是跨域请求的网络mock数据
    getMock(num) {
      this.msg = "小二正在努力，请耐心等待...";
      this.ifRequest = true;
      return this.$axios
        .get("http://localhost:4000/data?num=" + num)
        .then((res) => {
          this.ifRequest = false;
          return res.data.list;
        })
        .catch(() => {
          this.msg = "亲，网络请求出错啦！赶快检查吧...";
          return false;
        });
    },
    // 到达底部重新获取数据，触发这个事件是子组件下拉数据到底部以后再进行触发的
    // 同时，这里有一个非常重要的环节就是：如果我现在正在请求数据，那么用户在子组件中就算是再次触发下拉到底的操作也不会重复请求追加数据
    atBottom() {
      if (this.ifRequest) return;
      this.moreRequest();
    },
    async moreRequest() {
      //最多请求2万条数据
      if (this.listData.length >= 20000) {
        this.ifRequest = true;
        this.msg = "亲，到底啦！我是有底线的！";
        return;
      }
      this.ifRequest = true;
      let result = await this.getMock(50);
      this.listData = [...this.listData, ...result];
      this.ifRequest = false;
    },
  },
};
</script>

<style lang="scss" scoped>
.news-list {
  width: 100%;
  max-width: 800px;
  height: 100%;
  a {
    display: block;
    width: 100%;
    text-decoration: none;
  }
  .warpper {
    height: 100%;
  }
  .one-new {
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    justify-content: space-between;
    border-bottom: 1px solid #ddd;
    padding: 14px 10px 5px;
    .new-left {
      height: 80px;
      position: relative;
      h3 {
        padding: 0;
        margin: 0;
        font-size: 16px;
        text-align: justify;
        color: #555;
      }
      div {
        position: absolute;
        width: 100%;
        bottom: 10px;
        display: flex;
        flex-direction: row;
        flex-wrap: nowrap;
        justify-content: space-between;
        align-items: center;
        p {
          display: flex;
          flex-direction: row;
          flex-wrap: nowrap;
          justify-content: space-between;
          align-items: center;
          img {
            height: 16px;
          }
          span {
            font-size: 12px;
            color: #555;
            margin-left: 3px;
            margin-right: 3px;
          }
        }
        h4 {
          font-size: 12px;
          color: #888;
        }
      }
    }
    .new-right {
      margin-left: 10px;
      img {
        height: 68px;
      }
    }
  }
  .loading {
    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
    align-items: center;
    height: 60px;
    color: #2d8cf0;
  }
}
</style>
