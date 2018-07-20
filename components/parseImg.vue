<!--图片模板-->
<template>
  <div class="img-wrap">
    <div v-if="isPreview" class="preview-wrap">
      <img src="http://www.86y.org/images/loading.gif"/>
    </div>
    <img 
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
      isPreview: true,
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
      // console.log(e)
      const { mp } = e;
      const { currentTarget } = e;
      setTimeout(() => {
        const imgW = mp.detail.width;
        const imgH = mp.detail.height;
        const imgTypeGifIndex = currentTarget.id.indexOf('wx_fmt=gif');
        const ratio = 750 / imgW;
        let imageStyle;
        // imgTypeGifIndex大于-1说明是gif，则处理其图片宽高为固定值
        if (imgTypeGifIndex > -1) {
          imageStyle = `width: 640rpx; height: ${imgH * (640 / imgW)}rpx;`;
        } else if (imgTypeGifIndex > -1) {
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
        }
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
    this.getDp();
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
