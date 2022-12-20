<style lang="scss" scoped>
.content {
  transform: rotate(0deg);
  position: absolute;
  top: 0px;
  left: 345px;
}

p {
  font-size: 16px;
  font-weight: 500;
  color: #000;
  line-height: 1.7em;
}

.box {
  width: 100%;
  display: flex;
  position: relative;
}

.qr-box {
  text-align: center;
  margin-left: 20px;
  position: absolute;
  right: 5px;
  top: 5px;
}

.sub-text p {
  margin-top: 5px;
  font-size: 11px;
  line-height: 1em;
  font-weight: 600;
}

@page {
  margin: 10px;
}

@media print {
  .content,
  div,
  p {
    color: #000;
    font-family: "Microsoft YaHei";
    font-weight: 600;
  }
}
</style>
<template>
  <div class="content" :style="contentStyle">
    <div>
      测试打印页面
    </div>
  </div>
</template>

<script>
import {ipcRenderer} from "electron";

export default {
  name: "printView",
  data() {
    return {
      contentStyle: null,
      ticketInfo: {}
    };
  },
  components: {
  },
  mounted() {
    this.addEvent();
    let settingInfo = window.localStorage.getItem("setting");
    settingInfo = JSON.parse(settingInfo);
    const {printTop, printLeft, printRotate} = settingInfo;
    this.contentStyle = `{;top:${printTop}px;left:${printLeft}px;transform: rotate(${printRotate}deg);}`;
  },
  methods: {
    addEvent() {
      ipcRenderer.on("webview-print-render", (event, data) => {
        console.log("获取渲染请求，开始渲染:" + +new Date());
        this.ticketInfo = JSON.parse(JSON.stringify(data));
        this.$nextTick(() =>
            ipcRenderer.sendToHost("webview-print-do", this.ticketInfo)
        );
      });
    }
  }
};
</script>>
