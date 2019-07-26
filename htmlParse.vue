<template>
  <div>
    <template v-if="nodeList && nodeList.length">
      <block v-for="(item, index) in nodeList" :key="index">
        <htmlParse :item="item" />
      </block>
    </template>
  </div>
</template>

<script>
  import htmlParse from './components/htmlParse1';
  import html2json from './lib/html2json/index';
  export default {
    data() {
      return {
        nodeList: []
      }
    },
    props: {
      data: null,
      html: ''
    },
    components: {
      htmlParse
    },
    mounted() {
      if (this.data) {
        this.nodeList = this.data;
      } else {
        this.nodeList = html2json(this.html);
      }
    }
  };
</script>