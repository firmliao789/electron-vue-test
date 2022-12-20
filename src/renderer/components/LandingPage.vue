<template>
  <div id="wrapper">
    <main>
      <button @click="print" style="margin-top: 100px;margin-left: 100px;width: 200px;height: 200px">打印</button>
      <div>
        证件号码：
        <input style="width: 240px;height: 50px" type="text" placeholder="证件号码">
      </div>
    </main>
    <WebViewPrint ref="webViewPrint"/>
  </div>
</template>

<script>
import WebViewPrint from "../components/web-view-print";

export default {
  name: 'landing-page',
  components: {WebViewPrint},
  methods: {
    open(link) {
      this.$electron.shell.openExternal(link)
    },
    print() {
      this.$refs['webViewPrint'].print([{}]);
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');
</style>
