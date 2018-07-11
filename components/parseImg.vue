<!--图片模板-->
<template>
  <div style="width: 100%;height: auto;display: flex;justify-content: center;">
    <div v-if="isPreview" class="preview-wrap">加载中...</div>
    <img 
      v-else
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
        const ratio = 690 / imgW;
        let imageStyle;
        if (imgH / this.dp >= 690) {
          imageStyle = `width: 690rpx; height: ${imgH * ratio}rpx;`;
        } else {
          imageStyle = `width: ${imgW * this.dp}px; height: ${imgH * this.dp}px;`;
        }
        this.htmlParseImageStyle = imageStyle;
        if (!this.$root.htmlParseImageUrl) {
          this.$root.htmlParseImageUrl = [];
        }
        this.$root.htmlParseImageUrl.push(currentTarget.id);
      }, 0);
    }
  },
  mounted () {
    let winWidth = null;
    let dp = 1;
    try {
      winWidth = wx.getSystemInfoSync().windowWidth;
    } catch (e) {};
    if (winWidth) {
      dp = winWidth / 750;
    }
    this.dp = dp;
    setTimeout(() => {
      that.isPreview = false;
    }, 1000);
  }
};
</script>
<style scoped>
.preview-wrap {
  width: 400rpx;
  height: 300rpx;
  background-color: #e5e5e5;
  color: #838383;
  font-size: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
