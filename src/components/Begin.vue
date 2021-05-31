<template>
  <div class="begin-wrap" v-show="tipsShow">
    <div class="bg-box"></div>
    <div class="begin-box">
      <div class="begin-tips"></div>
      <div class="begin-btn" @click="begin"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Begin",
  data(){
    return{
      tipsShow: true,
    }
  },
  methods:{
    begin(){
      this.tipsShow = false
      this.$emit('videoStart', true)
    }
  }
};
</script>

<style scoped>

/*loading开始点击*/

.begin-wrap,
.begin-wrap .bg-box,
.begin-wrap .begin-box,
.begin-wrap .begin-box .begin-tips,
.begin-wrap .begin-box .begin-btn{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.begin-wrap {
    z-index: 100;
    background: #000;
}

.bg-box {
  background: url(../assets/img/bg01.png) no-repeat;
  background-size: 100% 100%;
}

.begin-wrap .begin-box {
  z-index: 2;
}

.begin-wrap .begin-box .begin-tips {
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

</style>
