<template>
  <div id="kindeditor-div">
    <editor id="editor_id" height="400px" width="1200px" :content="editorText"
            :afterChange="afterChange()"
            pluginsPath="/static/kindeditor/plugins/"
            :loadStyleMode="false"
            @on-content-change="onContentChange"></editor>
  </div>
</template>

<script>
import Vue from 'vue'
import VueKindEditor from 'vue-kindeditor'
import 'kindeditor/kindeditor-all-min.js'
import 'kindeditor/themes/default/default.css'

/* eslint-disable no-new */
Vue.use(VueKindEditor)

export default {
  name: 'kindeditor-div',
  data () {
    return {
      editorText: ''
    }
  },
  created () {
  },
  methods: {
    onContentChange (val) {
      this.editorText = val
    },
    afterChange () {
      // this.editorText = 'tsetst'
    }
  }
}
</script>
<!--<style>-->
  <!--#kindeditor-div {-->
    <!--float: left;-->
  <!--}-->
<!--</style>-->
