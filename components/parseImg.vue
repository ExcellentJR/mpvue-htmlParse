<!--图片模板-->
<template>
  <scroll-view class="img-wrap" enable-flex :scroll-x="isScroll" :style="{height: containerHeight, justifyContent: isScroll?'flex-start':'center'}">
    <div class="preview-wrap" :style="previewWrapStyle">
      <div v-if="isDisplay" class="loading-wrap" :class="[isPreview?'':(isShowImgHideAnimation?'img-hide':'')]" :style="htmlParseImageStyle">
        <image class="loading-img" src="http://www.86y.org/images/loading.gif"/>
      </div>
      <image
        class="detail-img"
        lazy-load
        @tap="previewImage(item.attr && item.attr.src)"
        @load="htmlParseImageLoad"
        :id="item.attr && item.attr.src"
        :src="item.attr && item.attr.src"
        :style="htmlParseImageStyle"
      />
    </div>
  </scroll-view>
</template>
<script>
export default {
  name: 'parseImg',
  data () {
    return {
      isPreview: true,
      isDisplay: true,
      htmlParseImageStyle: 'width: calc(100vw - 68rpx); height: 332rpx;',
      previewWrapStyle: 'width: calc(100vw - 68rpx); height: 332rpx;',
      winWidth: 375,
      perfectWidth: 682,
      imgPadding: 34,
      imgScrollWidth: 1000,
      containerHeight: '332rpx',
      isScroll: false
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
        if (wx) {
          this.winWidth = wx.getSystemInfoSync().windowWidth;
        } else {
          this.winWidth = uni.getSystemInfoSync().windowWidth;
        }
      } catch (e) {};
      if (this.winWidth) {
        dp = this.winWidth / 750;
      }
      this.dp = dp;
      this.perfectWidth = (this.winWidth - this.imgPadding) / this.dp;
    },
    // 富文图片点击预览
    previewImage (url) { 
      // 如果不允许预览则不预览
      if (this.disablePreviewImg) return false;
      if (!url) return false;
      if (wx) {
        wx.previewImage({
          current: url, // 当前显示图片的http链接
          urls: this.$root.htmlParseImageUrl // 需要预览的图片http链接列表
        });
      } else {
        uni.previewImage({
          current: url, // 当前显示图片的http链接
          urls: this.$root.htmlParseImageUrl // 需要预览的图片http链接列表
        });
      }
    },
    htmlParseImageLoad (e) { // 富文图片满屏适配
      // console.log(e)
      const that = this
      that.isPreview = false;
      const { mp } = e;
      const { currentTarget } = e;
      const imgW = mp.detail.width;
      const imgH = mp.detail.height;
      const isGif = currentTarget.id.includes('wx_fmt=gif') || currentTarget.id.includes('.gif');
      let imageStyle = ``;
      let timeid = setTimeout(() => {
        // 有padding和无padding分开处理
        if (that.imgPadding) {
          // 图片如果是gif，或者图片宽度达到640，则处理其图片宽高为固定值
          if (isGif || imgW > 639) {
            imageStyle = `width: ${that.perfectWidth}rpx; height: ${imgH * (that.perfectWidth / imgW)}rpx;`;
            that.containerHeight = `${imgH * (that.perfectWidth / imgW)}rpx`;
          } else {
            imageStyle = `width: ${imgW}rpx; height: ${imgH}rpx;`;
            that.containerHeight = `${imgH}rpx`;
          }
        } else {
          if (imgW > that.imgScrollWidth) {
            imageStyle = `width: ${imgW}rpx; height: ${imgH}rpx;`;
            that.containerHeight = `${imgH}rpx`;
            that.isScroll = true;
          } else {
            imageStyle = `width: 100vw; height: ${imgH * (that.perfectWidth / imgW)}rpx;`;
            that.containerHeight = `${imgH * (that.perfectWidth / imgW)}rpx`;
          }
        }
        that.htmlParseImageStyle = imageStyle;
        that.previewWrapStyle = `${imageStyle}background: none;`;
        if (!that.$root.htmlParseImageUrl) {
          that.$root.htmlParseImageUrl = [];
        }
        that.$root.htmlParseImageUrl.push(currentTarget.id);
        clearTimeout(timeid);
        let time = setTimeout(() => {
          that.isDisplay  = false;
          clearTimeout(time)
        }, that.imgPadding ? 0 : 300);
      }, 0);
    }
  },
  mounted () {
    // 关闭主容器内的loading
    if (this.$root.loading) this.$root.loading = false
    if (this.$root.hasOwnProperty('htmlParseImagePadding')) {
      this.imgPadding = this.$root.htmlParseImagePadding
    } else {
      this.imgPadding = 34
    }
    if (this.$root.hasOwnProperty('htmlParseImageScrollWidth')) {
      this.imgScrollWidth = this.$root.htmlParseImageScrollWidth
    } else {
      this.imgScrollWidth = 1000
    }
    if (this.$root.hasOwnProperty('isShowImgHideAnimation')) {
      this.isShowImgHideAnimation = this.$root.isShowImgHideAnimation
    } else {
      this.isShowImgHideAnimation = false
    }
    if (this.$root.hasOwnProperty('disablePreviewImg')) {
      this.disablePreviewImg = this.$root.disablePreviewImg
    } else {
      this.disablePreviewImg = false
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
.img-wrap .preview-wrap {
  width: 100vw;
  height: 332rpx;
  background-color: #f8f8f8;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}
.img-wrap .preview-wrap .loading-wrap {
  position: absolute;
  width: calc(100vw - 68rpx);
  height: 332rpx;
  background-color: #f8f8f8;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
  visibility: visible;
  opacity: 1;
}
.img-wrap .preview-wrap .img-hide {
  visibility: hidden;
  opacity: 0;
  transition-duration: .3s;
  -webkit-transition-duration: .3s;
}
.img-wrap .preview-wrap .loading-wrap .loading-img {
  width: 60rpx;
  height: 60rpx;
}
.img-wrap .preview-wrap .detail-img {
  z-index: 99;
}
</style>