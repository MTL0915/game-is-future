<template>
  <div id="qcenv"></div>
</template>

<script>

import * as THREE from 'three/build/three.module.js';

import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

export default {
  name: "QCEnv",
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      controls: null,
    };
  },
  methods:{
    init(){
      const container = document.getElementById("qcenv");

      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      container.appendChild(this.renderer.domElement);

      this.scene = new THREE.Scene();

      this.camera = new THREE.PerspectiveCamera(
        60,
        window.innerWidth / window.innerHeight,
        0.1,
        100
      );
      // 相机会在它自己的位置，往坐标轴（0,0,0）看去
      this.camera.position.z = -0.05;
      this.camera.position.y = 0;
      this.camera.position.x = -0.2;

      this.controls = new OrbitControls(this.camera,this.renderer.domElement);
      this.controls.enableZoom = false;
      this.controls.enablePan = false;
      this.controls.enableDamping = true;
      this.controls.rotateSpeed = -0.25;

      // 坐标轴
      var axisHelper = new THREE.AxisHelper(250);
      this.scene.add(axisHelper);


      const textures = this.getTexturesFromAtlasFile(
        "/static/qccolor5.jpg",
        6
      );

      const materials = [];

      for (let i = 0; i < 6; i++) {
        materials.push(new THREE.MeshBasicMaterial({ map: textures[i] }));
      }

      const skyBox = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 1), materials);
      skyBox.geometry.scale(1, 1, -1);
      skyBox.geometry.translate(0, 0, 0)
      this.scene.add(skyBox);

      window.addEventListener("resize", this.onWindowResize); 
    },
    
    getTexturesFromAtlasFile(atlasImgUrl, tilesNum) {
      const textures = [];
      for (let i = 0; i < tilesNum; i++) {
        textures[i] = new THREE.Texture();
      }
      const imageObj = new Image();
      imageObj.onload = function() {
        let canvas, context;
        const tileWidth = imageObj.height;
        for (let i = 0; i < textures.length; i++) {
          canvas = document.createElement("canvas");
          context = canvas.getContext("2d");
          canvas.height = tileWidth;
          canvas.width = tileWidth;
          context.drawImage(
            imageObj,
            tileWidth * i,
            0,
            tileWidth,
            tileWidth,
            0,
            0,
            tileWidth,
            tileWidth
          );
          textures[i].image = canvas;
          textures[i].needsUpdate = true;
        }
      };
      imageObj.src = atlasImgUrl;
      return textures;
    },

    onWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();

      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },

    animate() {
      requestAnimationFrame(this.animate);

      this.controls.update(); // required when damping is enabled

      this.renderer.render(this.scene, this.camera);
    }

  },

  mounted() {
    this.init();
    this.animate()
  }
}


</script>
