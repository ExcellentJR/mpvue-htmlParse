# mpvue-richtextParse 微信小程序富文本解析 - mpvue版

> 目前只支持 node 数组，在后端或者传递值之前做 hmtl2json 处理
> 
> 对于长富文本来说，在小程序端解析比较卡。建议在后端直接处理成 json 格式传递给小程序渲染


## 基本使用方法

1. 安装
``` bash
npm i mpvue-richtextparse
```

2. 在 mpvue 中使用

``` vue
<template>
  <div>
    <richtextParse :data="content"  />
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
      html: '<div style="color: red; font-size: 12px;">html test</div>'
      content: [...nodeList]
    }
  }
}
</script>
```

## 相关小程序预览

![相关截图](images/hdll.jpg)
