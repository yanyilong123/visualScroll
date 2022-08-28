<template>
  <div class="scroll-container" @scroll.passive="handleScroll" ref="scrollContainer">
     <!-- 
      对应的参数介绍
      needReanderList: 真正渲染到页面的数据列表
      paddingStyle：上下空白占位
    -->
    <slot :needReanderList="needReanderList" :paddingStyle="paddingStyle"/>
  </div>
</template>

<script>
export default {
  props: {
    //已获取到的所有数据列表
    allDataList: { default: () => [], type: Array },
    //单行显示高度
    blockHeight: { default: () => 100, type: Number },
  },
  data() {
    return {
      //上下缓冲区数量
      bufferSize: 1,
      //屏幕容积数量
      screenNum: 5,
      //可视区域的开始位置索引
      currentBlockIndex: 0,
    };
  },
  computed: {
    //渲染区域的开始位置索引
    startIndex() {
      return Math.max(0, this.currentBlockIndex - this.bufferSize)
    },
    //渲染区域的结束位置索引
    endIndex() {
      let Index = this.currentBlockIndex + this.screenNum + this.bufferSize
      return Math.min(this.allDataList.length, Index)
    },
    //计算上下空白占位
    paddingStyle() {
      return {
        "padding-top": this.startIndex * this. blockHeight + "px",
        "padding-bottom": (this.allDataList.length - this.endIndex) * this. blockHeight + "px"
      };
    },
    //计算真正渲染到页面的数据集
    needReanderList() {
      return this.allDataList.slice(this.startIndex, this.endIndex )
    }
  },
  mounted() {
    //监听屏幕变化，动态获取当前屏幕最大容积数量
    this.myresize();
    window.onresize = this.myresize;
    window.orientationchange = this.myresize;
  },
  methods: {
    //监听屏幕变化，动态获取屏幕最大容积数量，直接使用对应渲染API体验效果更佳
    myresize() {
      this.screenNum = Math.floor(window.innerHeight / this.blockHeight) + 2;
      this.$refs.scrollContainer.style.height = window.innerHeight + "px";
    },
    //监听当前容器的滚动事件
    handleScroll() {
      //第一步，我们获取当前容器在scoll事件中距离顶部的位移
      let scrollHeight = this.$refs.scrollContainer.scrollTop;
      //第二步，根据当前位移,计算可视区域的开始位置索引
      let currentBlockIndex = Math.floor(scrollHeight / this.blockHeight);
      // 若可视区域的开始位置索引没有发生变化则不重新设置，防止资源消耗提高性能
      if (this.currentBlockIndex !== currentBlockIndex) {
        this.currentBlockIndex = currentBlockIndex
      }
      //第三步：判断是否到达底部，如果到达底部则触发新的数据请求
      if (this.endIndex === this.allDataList.length) {
        this.$emit("bottom");
      }
    },
  }
};
</script>

<style lang="scss" scoped>
.scroll-container {
  width: 100%;
  height: 100%;
  overflow-x: hidden;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
</style>
