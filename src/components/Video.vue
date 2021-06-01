<template>
  <div class="video-wrap" v-show="videoShow">
    <video
      src="../assets/img/start.mp4"
      id="video"
      width="100%"
      height="100%"
      preload="auto"
      x5-playsinline="" 
      playsinline="" 
      webkit-playsinline=""
    ></video>
    <div class="loading-wrapper" v-show="loadingShow">
      <Loading />
    </div>
  </div>
</template>

<script>
import Loading from './Loading'

export default {
  props:{
    videoStatus:Boolean
  },
  name: "Video",
  data(){
    return{
      videoShow: false,
      loadingShow: true
    }
  },
  components:{
    Loading
  },
  watch:{
    videoStatus: function(){
      this.videoShow = this.videoStatus
      const video = document.getElementById('video')
      const that = this
      video.addEventListener('timeupdate',function(){
        if(video.currentTime > 0){
          that.loadingShow = false
        }        
      });  
      video.play()       
      video.addEventListener("ended",function(){
        that.videoShow = false
      })

    }
  }
};
</script>

<style scoped>

.video-wrap,
#video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.video-wrap{
  background-color: black;
}
.loading-wrapper{
  width: 100%;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}
</style>
