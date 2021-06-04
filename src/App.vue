<template>
  <div id="app">
    <Begin @videoStart="conVideoStart" />
    <Video :videoStatus="videoStatus"/>
    <!-- <Env /> -->
    <QCEnv @photoShow="conPhotoShow"/>
    <Photo :photoStatus="photoStatus" :picName="picName" @clickCancel="reClickCancel"/>
  </div>
</template>

<script>
import Begin from "./components/Begin";
import Video from "./components/Video";
// import Env from "./components/Env";
import QCEnv from "./components/QCEnv";
import Photo from "./components/Photo";

export default {
  name: "App",
  components: {
    Begin,
    Video,
    // Env,
    QCEnv,
    Photo
  },
  data(){
    return{
      videoStatus: false,
      photoStatus: false,
      picName : null
    }
  },
  methods:{
    conVideoStart(videoGo){
      this.videoStatus = videoGo
    },
    conPhotoShow(picShow, name){
      this.photoStatus = picShow
      this.picName = name
    },
    reClickCancel(reProps){
      this.photoStatus = !reProps
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
