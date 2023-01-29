<template>
  <div
    class="scroll-container"
    @scroll="handleScroll"
    ref="scrollContainer"
    :style="containerHeight"
  >
    <div class="warpper" ref="wapperBox" :style="fillBlankPading">
      <div v-for="(item, index) in needRenderList" :key="index">
        <slot :thisItem="item"></slot>
      </div>
      <div class="loading" v-if="onRequesting">
        <div>{{ msg }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    // 传入所有列表数据
    allDataList: { default: () => [], type: Array },
    // 上下缓存区数目
    bufferSize: { default: () => 5, type: Number },
    // 单行显示高度
    blockHeight: { default: () => 100, type: Number },
    // 数据显示加载的提示语
    msg: { default: () => '小二正在努力，请耐心', type: String },
    // 虚拟滚动列表设置固定高度
    propHeight: { default: () => window.innerHeight, type: Number },
    // 是否正在请求批量数据状态...
    onRequesting: { default: () => true, type: Boolean },
    // 触底函数
    bottom: { type: Function },
  },
  data() {
    return {
      // 记录当前列表上卷去的高度，当路由再次激活时使用它回归原位
      scrollHeight: 0,
      // 需要渲染列表的起始index
      startIndex: 0,
      // 需要渲染的数据
      needRenderList: [],
      // 节流
      isScoll: true,
    };
  },

  computed: {
    // 虚拟滚动列表容器高度
    containerHeight() {
      return {
        height: this.propHeight + 'px',
      };
    },
    fillBlankPading() {
      return {
        'padding-top': this.startIndex * this.blockHeight + 'px',
        'padding-bottom':
          (this.allDataList.length - this.endIndex) * this.blockHeight + 'px',
      };
    },
    needRenderSize() {
      return this.bufferSize * 2 + this.screenNum;
    },
    // 屏幕的最大容积数量
    screenNum() {
      return ~~(this.propHeight / this.blockHeight) + 2;
    },
    endIndex() {
      /* eslint-disable */
      let endIndex = this.startIndex + this.screenNum;
      if (!this.allDataList[endIndex]) {
        endIndex = this.allDataList.length - 1;
      }
      return endIndex;
    },
  },
  watch: {
    allDataList() {
      this.changeBufferList();
    },
  },
  mounted() {
    // 监听屏幕变化，动态获取当前屏幕最大容积
    /* eslint-disable */
    this.myresize();
    window.onresize = this.myresize;
    window.onorientationchange = this.myresize;
  },
  methods: {
    // 监听屏幕变化
    myresize() {
      // this.screenNum = ~~(this.$refs.scrollContainer.innerHeight / this.blockHeight) + 2
      // this.$refs.scrollContainer.style.height = window.innerHeight + "px"
    },
    throttle(fn, delay) {
      let pre = 0;
      return function() {
        let now = Date.now();
        if (now - pre > delay) {
          fn.apply(this, Arguments);
          pre = now;
        }
      };
    },
    handleScroll1() {
      handleScrollthis.throttle(handleScroll, 1000);
    },
    handleScroll() {
        this.changeBufferList();
    },
    changeBufferList() {
      // 1、获取当前容器上滚过的距离
      this.scrollHeight = this.$refs.scrollContainer.scrollTop;
      // 2、根据上卷高度计算currentIndex
      this.startIndex = ~~(this.scrollHeight / this.blockHeight);
      console.log(this.startIndex, this.endIndex);
      // 3、根据startIndex和endIndex进行切割
      this.needRenderList = this.allDataList.slice(
        this.startIndex,
        this.endIndex
      );
    },
  },
};
</script>

<style lang="scss" scoped>
.scroll-container {
  overflow: auto;
  /* height: 300px; */
  .warpper {
    overflow: auto;
  }
}
</style>
