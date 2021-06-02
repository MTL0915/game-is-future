<template>
  <div id="qc-env"></div>
</template>

<script>
import * as THREE from "three/build/three.module.js";

import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

export default {
  name: "QCEnv",
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      controls: null,
      raycaster: null,
      mouse: null
    };
  },
  methods: {
    init() {
      const container = document.getElementById("qc-env");

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

      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      this.controls.enableZoom = true; // 滚轮是否可控制
      this.controls.enablePan = false; //是否可平移
      this.controls.enableDamping = true;
      this.controls.enableRotate = true;
      this.controls.rotateSpeed = -0.25;
      // this.controls.dispose () //移除所有的事件监听
      // console.log(this.controls)

      // 坐标轴
      var axisHelper = new THREE.AxisHelper(250);
      this.scene.add(axisHelper);

      const textures = this.getTexturesFromAtlasFile("/static/qccolor.jpg", 6);

      const materials = [];

      for (let i = 0; i < 6; i++) {
        materials.push(new THREE.MeshBasicMaterial({ map: textures[i] }));
      }

      const skyBox = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 1), materials);
      skyBox.geometry.scale(1, 1, -1);
      skyBox.geometry.translate(0, 0, 0);
      this.scene.add(skyBox);

      window.addEventListener("resize", this.onWindowResize);
      window.addEventListener("click", this.onMouseClick , true);

      // 测试添加正方体1
      const cube1 = new THREE.BoxGeometry(0.03, 0.03, 0.03);
      const material1 = new THREE.MeshBasicMaterial({color:0x0000ff})
      const mesh1 = new THREE.Mesh(cube1, material1);
      mesh1.position.x = 0.5;
      mesh1.position.y = -0.1;
      mesh1.position.z = 0;
      mesh1.updateMatrix();
      this.scene.add(mesh1);  

      // 测试添加正方体2
      const cube2 = new THREE.BoxGeometry(0.03, 0.03, 0.03);
      const material2 = new THREE.MeshBasicMaterial({color:0x0000ff})
      const mesh2 = new THREE.Mesh(cube2, material2);
      mesh2.position.x = 0;
      mesh2.position.y = 0;
      mesh2.position.z = 0.2;
      mesh2.updateMatrix();
      this.scene.add(mesh2); 

    },

    // 射线获取对象

    onMouseClick(event) {
        //声明raycaster和mouse变量
        this.raycaster = new THREE.Raycaster();
        this.mouse = new THREE.Vector2();

        //通过鼠标点击的位置计算出raycaster所需要的点的位置，以屏幕中心为原点，值的范围为-1到1.

        this.mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
        this.mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

        // 通过鼠标点的位置和当前相机的矩阵计算出raycaster
        this.raycaster.setFromCamera( this.mouse, this.camera );

        // 获取raycaster直线和所有模型相交的数组集合
        const intersects = this.raycaster.intersectObjects( this.scene.children );

        console.log(intersects)
        
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
    },
  },

  mounted() {
    this.init();
    this.animate();
  },
};
</script>

<style scoped>

</style>
