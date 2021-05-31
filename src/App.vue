<template>
  <div id="app">
    <Begin @videoStart="conVideoStart" />
    <Video :videoStatus="videoStatus"/>
    <!-- <Env /> -->
    <QCEnv />
  </div>
</template>

<script>
import Begin from "./components/Begin";
import Video from "./components/Video";
// import Env from "./components/Env";
import QCEnv from "./components/QCEnv";

export default {
  name: "App",
  components: {
    Begin,
    Video,
    // Env,
    QCEnv
  },
  data(){
    return{
      videoStatus: false
    }
  },
  methods:{
    conVideoStart(videoGo){
      this.videoStatus = videoGo
    }
  },

  // 禁止放大缩小
  mounted() {
    document.documentElement.addEventListener(
      "touchstart",
      function(event) {
        if (event.touches.length > 1) {
          event.preventDefault();
        }
      },
      { passive: false }
    );
    // 禁止双击放大
    let lastTouchEnd = 0;
    document.documentElement.addEventListener(
      "touchend",
      function(event) {
        var now = Date.now();
        if (now - lastTouchEnd <= 300) {
          event.preventDefault();
        }
        lastTouchEnd = now;
      },
      { passive: false }
    );
  },
};
</script>

<style>
body {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
#app {
  width: 100%;
  height: 100%;
  background-color: black;
}
</style>
