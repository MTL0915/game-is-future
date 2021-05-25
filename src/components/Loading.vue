<template>
  <div class="m-wrap">
    <div class="m-loading" v-show="tipsShow">
      <div class="bg-box"></div>
      <div class="begin-box">
        <div class="begin-tips"></div>
        <div class="btn-begin" @click="begin"></div>
      </div>
    </div>
    <div class="m-index" v-show="vedioShow">
      <div class="video-box">
        <video
          src="../assets/img/qc.mp4"
          id="video"
          width="100%"
          height="100%"
          preload="none"
          x5-playsinline=""
          playsinline=""
          webkit-playsinline=""
        ></video>
        <div class="btn-skip"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Loading",
  data:function(){
    return{
      vedioShow: false,
      tipsShow: true,
    }
  },
  methods:{
    begin(){
      this.tipsShow = false
      this.vedioShow = true
      const video = document.getElementById('video')
      video.play()
    }
  }
};
</script>

<style scoped>

/*loading开始点击*/

.m-loading,
.m-loading .bg-box {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.m-loading {
    z-index: 100;
    background: #000;
}

.bg-box {
  background: url(../assets/img/bg01.png) no-repeat;
  background-size: 100% 100%;
}

.m-loading .begin-box {
  position: absolute;
  z-index: 2;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.m-loading .begin-box .begin-tips {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: url(../assets/img/jiantou.png) no-repeat;
  background-size: 100% 100%;
  -webkit-animation: tips 1.4s linear infinite;
  animation: tips 1.4s linear infinite;
}

@keyframes tips {
  0% {
    transform: scale(1);
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.02);
    transform: scale(1.02);
    opacity: 0.6;
  }
  to {
    transform: scale(1);
    transform: scale(1);
    opacity: 1;
  }
}

@-webkit-keyframes tips {
  0% {
    -webkit-transform: scale(1);
    transform: scale(1);
    opacity: 1;
  }
  50% {
    -webkit-transform: scale(1.02);
    transform: scale(1.02);
    opacity: 0.6;
  }
  to {
    -webkit-transform: scale(1);
    transform: scale(1);
    opacity: 1;
  }
}

/*H5进行中*/

.m-index,
.m-index .video-box,
.m-loading .begin-box .btn-begin {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
