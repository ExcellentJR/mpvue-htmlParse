<!--图片模板-->
<template>
  <div class="img-wrap">
    <div v-if="!willInViewport" class="preview-static-wrap"></div>
    <div v-else-if="willInViewport&&isPreview" class="preview-wrap">
      <img src="http://www.86y.org/images/loading.gif"/>
    </div>
    <img 
      v-if="willInViewport"
      class="html-parse__img" 
      @tap="htmlParseImageTab(item.attr && item.attr.src)"
      @load="htmlParseImageLoad"
      :id="item.attr && item.attr.src"
      :src="item.attr && item.attr.src"
      :style="htmlParseImageStyle"/>
  </div>
</template>
<script>
export default {
  name: 'parseImg',
  data () {
    return {
      willInViewport: false,
      isPreview: false,
      htmlParseImageStyle: ''
    };
  },
  props: {
    item: {
      type: Object,
      default: {}
    }
  },
  methods: {
    getDp () {
      let winWidth = null;
      let dp = 1;
      try {
        winWidth = wx.getSystemInfoSync().windowWidth;
      } catch (e) {};
      if (winWidth) {
        dp = winWidth / 750;
      }
      this.dp = dp;
    },
    htmlParseImageTab (url) { // 富文图片点击预览
      if (!url) {
        return false;
      }
      wx.previewImage({
        current: url, // 当前显示图片的http链接
        urls: this.$root.htmlParseImageUrl // 需要预览的图片http链接列表
      });
    },
    htmlParseImageLoad (e) { // 富文图片满屏适配
      const { mp } = e;
      const { currentTarget } = e;
      setTimeout(() => {
        const imgW = mp.detail.width;
        const imgH = mp.detail.height;
        const ratio = 750 / imgW;
        let imageStyle;
        if (imgW > 750) {
          imageStyle = `width: 750rpx; height: ${imgH * ratio}rpx;`;
        } else {
          // imageStyle = `width: ${imgW * this.dp}rpx; height: ${imgH * this.dp}rpx;`;
          imageStyle = `width: ${imgW}rpx; height: ${imgH}rpx;`;
        }
        // if ((imgH / this.dp) === 750 || (imgH / this.dp) > 750) {
        //   imageStyle = `width: 750rpx; height: ${imgH * ratio}rpx;`;
        // } else {
        //   imageStyle = `width: ${imgW * this.dp}px; height: ${imgH * this.dp}px;`;
        // }
        this.isPreview = false;
        this.htmlParseImageStyle = imageStyle;
        if (!this.$root.htmlParseImageUrl) {
          this.$root.htmlParseImageUrl = [];
        }
        this.$root.htmlParseImageUrl.push(currentTarget.id);
      }, 0);
    }
  },
  mounted () {
    const that = this;
    that.getDp();
    // 懒加载
    let intersectionObserver = wx.createIntersectionObserver();
    intersectionObserver.relativeToViewport({bottom: 100}).observe('.img-wrap', (res) => {
      console.log('我快要出来了！')
      if (res.boundingClientRect.top > 0) {
        intersectionObserver.disconnect()
        that.willInViewport = true
        that.isPreview = true
      }
    })
  }
};
</script>
<style scoped>
.img-wrap {
  width: 100%;
  height: auto;
  display: flex;
  justify-content: center;
}
.preview-static-wrap {
  width: 640rpx;
  height: 400rpx;
  background-color: #f5f5f5;
}
.preview-wrap {
  width: 640rpx;
  height: 400rpx;
  background-color: #f5f5f5;
  display: flex;
  align-items: center;
  justify-content: center;
}
.preview-wrap img {
  width: 60rpx;
  height: 60rpx;
}
</style>
