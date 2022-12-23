<style>
</style>
<template>
  <div style="display:none;">
    <webview
        ref="printWebview"
        id="printWebview"
        src="index.html#/print-view"
        nodeintegration
        style="visibility: hidden;"
    ></webview>
  </div>
</template>

<script>
export default {
  name: "webViewPrint",
  data() {
    return {};
  },
  mounted() {
    this.addEventListener();
  },
  methods: {
    addEventListener() {
      const webview = document.querySelector("#printWebview");
      webview.addEventListener("did-finish-load", msg =>
          console.log("did-finish-load:", msg)
      );
      webview.addEventListener("load-commit", msg =>
          console.log("load-commit:", msg)
      );

      webview.addEventListener("did-fail-load", msg =>
          console.log("did-fail-load：", msg)
      );

      webview.addEventListener("ipc-message", async event => {
        if (event.channel === "webview-print-do") {
          try {
            console.log("开始打印", +new Date());
            const ret = await webview.print({
              silent: true,
              printBackground: true
              //deviceName: name
            });
            this.$bus.emit("printSuccess", event.args[0]);
            console.log("打印结束", +new Date());
            this.render();
            this.updatePrintStatus(event.args[0].id);
            global.settingInfo.printNumber = global.settingInfo.printNumber - 1;
            window.localStorage.setItem(
                "setting",
                JSON.stringify(global.settingInfo)
            );
          } catch (err) {
            console.log("打印出错：", err);
          }
        }
      });
      webview.addEventListener("console-message", msg => {
        console.log("console-message", msg.message);
      });
    },
    render() {
      console.log("render:", this.prints);
      // if (this.prints.length > 0) {
      //   this.showLoading({ text: "系统正在出票..." });
      const data = this.prints.shift();
      console.log("发送渲染数据", data.id, +new Date());
      setTimeout(async () => {
        const ret = await webview.print({
          silent: true,
          printBackground: true
          //deviceName: name
        });
        console.log("开始打印");
      }, 5000)

      // this.$refs["printWebview"].send("webview-print-render", data);
      // } else {
      //   this.hideLoading();
      // }
    },
    print(list) {
      this.prints = [...list];
      this.render();
    },
    //更新服务端打印状态
    updatePrintStatus(Id) {
      this.$http({
        url: "setPrint",
        data: {
          Id,
          mid: global.settingInfo.MID
        }
      });
    }
  }
};
</script>
