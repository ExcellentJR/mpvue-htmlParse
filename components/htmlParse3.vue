<template>
  <!--文本节点-->
  <block v-if="item.node == 'text' && item.text"><text>{{item.text}}</text></block>
  <!--其他点-->
  <block v-else-if="item.node == 'element'">
    <!--图片节点-->
    <block v-if="item.tag == 'img'">
      <parse-img :item="item" />
    </block>
    <!--a链接节点-->
    <block v-else-if="item.tag == 'a'">
      <div class="html-parse__a" @tap="htmlParseLinkTab" :style="item.attr && item.attr.style">
        <block v-for="(item, index) in item.child" :key="index">   
          <htmlParse :item="item" />
        </block>
      </div>
    </block>
    <!--换行-->
    <block v-else-if="item.tag == 'br'">
      <parse-br />
    </block>
    <!--wx video节点-->
    <block v-else-if="item.tag == 'video' || (item.tag == 'iframe' && item.attr && item.attr.class == 'video_iframe')">
      <parse-video :item="item"/>
    </block>
    <!--其他节点-->
    <div v-else :class="'html-parse__element html-parse__' + item.tag" :style="item.attr && item.attr.style">
      <block v-for="(item, index) in item.child" :key="index">
        <htmlParse :item="item" />
      </block>
    </div>
  </block>
</template>

<script>
import htmlParse from './htmlParse4';
import parseImg from './parseImg';
import parseBr from './parseBr';
import parseVideo from './parseVideo';

export default {
  name: 'wxParse',
  props: {
    item: {
      type: Object,
      default: {}
    }
  },
  components: {
    htmlParse,
    parseImg,
    parseBr,
    parseVideo
  }
};
</script>
