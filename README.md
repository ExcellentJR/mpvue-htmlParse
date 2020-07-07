# mpvue-richtextParse 微信小程序富文本解析 - mpvue/uni-app版

> 目前支持 node 数组，在后端或者传递值之前做 hmtl2json 处理
> 对于html字符串格式的长富文本解析来说，不建议字符串内容过长。


## 基本使用方法

1. 安装
``` bash
npm i mpvue-richtextparse
```

2. 在 mpvue 中使用

``` vue
<template>
  <div>
    <!-- node数组方式 -->
    <richtextParse :data="content"  />
    <!-- html字符串解析方式 -->
    <richtextParse :html="html"  />
  </div>
</template>

<script>
import richtextParse from 'mpvue-richtextparse';

export default {
  components: {
    richtextParse
  },
  data () {
    return {
      html: '<div style="color: red; font-size: 12px;">html test</div>',
      content: [...nodeList],
      htmlParseImagePadding: 0, // 解析图片时，图片的内边距大小，默认为34rpx
      htmlParseImageScrollWidth: 1500, // 当图片宽度大于该值时，图片将可以横向滚动，默认1000
      isShowImgHideAnimation: false // 是否显示图片loading效果隐藏的动画，默认false
    }
  }
}
</script>
```
3. 感谢[suinia](https://github.com/suinia)的项目[mpvue-htmlParse](https://github.com/suinia/mpvue-htmlParse)
