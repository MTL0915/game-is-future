<template>
  <div id="app">
    <Loading />
  </div>
</template>

<script>
import Loading from "./components/Loading.vue";

export default {
  name: "App",
  components: {
    Loading,
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
}
</style>
