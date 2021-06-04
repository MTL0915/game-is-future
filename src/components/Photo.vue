<template>
  <div class="photo" v-show="photoShow" @click="hidePhoto">
    <div class="photo-box"></div>
  </div>
</template>

<script>
export default {
  props:{
    photoStatus: Boolean,
    picName: String
  },
  name: "Photo",
  data(){
    return{
      photoShow: false,
    }
  },
  methods:{
    hidePhoto(){
      this.photoShow = false
      this.$emit('clickCancel', this.photoStatus)
    }
  },
  watch:{
    photoStatus: function(){
      this.photoShow = this.photoStatus
      document.getElementsByClassName('photo-box')[0].style.backgroundImage = "url(/static/" + this.picName +".jpg)"
    }
  }
};
</script>


<style scoped>
.photo{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color:rgba(0,0,0,.5);
}

.photo-box {
  width:60%;
  height:60%;
  margin:0 auto;
  background: url(../assets/img/cj01.jpg) no-repeat;
  background-size: 100% 100%;
  margin-top:20%;
  -webkit-animation: fall 3s 1;
  animation: fall 3s 1;
}

@keyframes fall {
  from, 25% {
    -webkit-transform: rotate(20deg) translateX(0);
    transform: rotate(20deg) translateX(0);
    opacity: 0;
    -webkit-animation-timing-function: cubic-bezier(.07, 2.02, .67, .57);
    animation-timing-function: cubic-bezier(.07, 2.02, .67, .57);
  }

  75%, to {
    -webkit-transform: rotate(0) translateX(0);
    transform: rotate(0) translateX(0);
    opacity: 1;
  }
}




</style>
