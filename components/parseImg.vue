<!--图片模板-->
<template>
  <div class="img-wrap">
    <div v-if="isPreview" class="preview-wrap">
      <img src="http://www.86y.org/images/loading.gif"/>
    </div>
    <image
      lazy-load
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
      htmlParseImageStyle: '',
      winWidth: 375,
      perfectWidth: 682,
      imgPadding: 34
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
      this.winWidth = null;
      let dp = 1;
      try {
        this.winWidth = wx.getSystemInfoSync().windowWidth;
      } catch (e) {};
      if (this.winWidth) {
        dp = this.winWidth / 750;
      }
      this.dp = dp;
      this.perfectWidth = (this.winWidth - this.imgPadding) / this.dp;
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
      const that = this
      const { mp } = e;
      const { currentTarget } = e;
      const imgW = mp.detail.width;
      const imgH = mp.detail.height;
      const isGif = currentTarget.id.includes('wx_fmt=gif');
      let imageStyle = ``;
      setTimeout(() => {
        // isGif为true说明是gif，则处理其图片宽高为固定值
        if (isGif || imgW > 639) {
          imageStyle = `width: ${that.perfectWidth}rpx; height: ${imgH * (that.perfectWidth / imgW)}rpx;`;
        } else {
          imageStyle = `width: ${imgW}rpx; height: ${imgH}rpx;`;
        }
        that.htmlParseImageStyle = imageStyle;
        that.isPreview = false;
        if (!that.$root.htmlParseImageUrl) {
          that.$root.htmlParseImageUrl = [];
        }
        that.$root.htmlParseImageUrl.push(currentTarget.id);
      }, 0);
    }
  },
  mounted () {
    // 关闭主容器内的loading
    this.$root.loading = false
    if (this.$root.hasOwnProperty('htmlParseImagePadding')) {
      this.imgPadding = this.$root.htmlParseImagePadding
    } else {
      this.imgPadding = 34
    }
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
  width: 682rpx;
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
