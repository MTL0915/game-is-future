<template>
  <div id="qc-env"></div>
</template>

<script>
import * as THREE from "three/build/three.module.js";

import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { OBJLoader } from '../../node_modules/three/examples/jsm/loaders/OBJLoader.js';
import { MTLLoader } from '../../node_modules/three/examples/jsm/loaders/MTLLoader.js';

export default {
  name: "QCEnv",
  data() {
    return {
      camera: null,
      scene: null,
      scene2: null,
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
      this.camera.position.x = -0.3;

      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      this.controls.enableZoom = true; // 滚轮是否可控制
      this.controls.enablePan = false; //是否可平移
      this.controls.enableDamping = true;
      this.controls.enableRotate = true;
      this.controls.rotateSpeed = -0.25;
      // this.controls.dispose () //移除所有的事件监听
      // console.log(this.controls)

      // 创建点光源照着猫
      let light = new THREE.DirectionalLight(0xffffff);
      light.position.set(0.2, 0.2, 0.2);
      this.scene.add(light);
      let light2 = new THREE.DirectionalLight(0xffffff);
      light2.position.set(-80, -100, -50);
      this.scene.add(light2);
      // 环境光
      var ambient = new THREE.AmbientLight(0xffffff);
      this.scene.add(ambient);

      // 坐标轴
      // var axisHelper = new THREE.AxisHelper(0.5);
      // this.scene.add(axisHelper);

      const textures = this.getTexturesFromAtlasFile("/static/qccolor.jpg", 6);

      const materials = [];

      for (let i = 0; i < 6; i++) {
        materials.push(new THREE.MeshBasicMaterial({ map: textures[i] }));
      }

      const skyBox = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 1), materials);
      skyBox.geometry.scale(1, 1, -1);
      skyBox.geometry.translate(0, 0, 0);
      this.scene.add(skyBox);

      // 事件触发
      window.addEventListener("resize", this.onWindowResize);
      window.addEventListener("click", this.onMouseClick , true);
      this.controls.addEventListener("start", this.onMouseClick ,false);

      // 测试添加正方体1
      const cube1 = new THREE.BoxGeometry(0.03, 0.03, 0.03);
      const material1 = new THREE.MeshBasicMaterial({color:0x0000ff})
      const mesh1 = new THREE.Mesh(cube1, material1);
      mesh1.position.x = 0.5;
      mesh1.position.y = 0.2;
      mesh1.position.z = 0.2;
      mesh1.updateMatrix();
      mesh1.name = 'cube1'
      console.log(mesh1)
      this.scene.add(mesh1);  

      // 测试添加正方体2(环境变化)
      const cube2 = new THREE.BoxGeometry(0.03, 0.03, 0.03);
      const material2 = new THREE.MeshBasicMaterial({color:0x00ff00})
      const mesh2 = new THREE.Mesh(cube2, material2);
      mesh2.position.x = 0.45;
      mesh2.position.y = -0.1;
      mesh2.position.z = 0;
      mesh2.updateMatrix();
      mesh2.name = 'cube2'
      this.scene.add(mesh2); 

      // 测试猫模型
      let that=this;
      let objloader=new OBJLoader();
      let mtlloader=new MTLLoader();
      mtlloader.load('/static/Maneki_Nekodimo.mtl', function(materials) {
        // 返回一个包含材质的对象MaterialCreator
        // console.log(materials);
        //obj的模型会和MaterialCreator包含的材质对应起来
        objloader.setMaterials(materials);
        objloader.load('/static/Maneki_Nekodimo.obj', function(obj) {
          let cat=obj;
          cat.scale.set(0.001,0.001,0.001)
          cat.children[0].name = 'cat'
          that.scene.add(cat);
        })
      })

      // 测试飞船模型
      let objloader2=new OBJLoader();
      let mtlloader2=new MTLLoader();
      mtlloader2.load('/static/feichuan.mtl', function(materials) {
        // 返回一个包含材质的对象MaterialCreator
        // console.log(materials);
        //obj的模型会和MaterialCreator包含的材质对应起来
        objloader2.setMaterials(materials);
        objloader2.load('/static/feichuan.obj', function(obj) {
          let feichuan=obj;
          feichuan.scale.set(0.0001,0.0001,0.0001)
          feichuan.children[0].name = 'feichuan'
          that.scene.add(feichuan);
        })
      })


      // 测试添加正方体3
      const cube3 = new THREE.BoxGeometry(0.03, 0.03, 0.03);
      const material3 = new THREE.MeshBasicMaterial({color:0xFF00FF})
      material3.transparent = true ;
      material3.opacity = 0.5 ;
      const mesh3 = new THREE.Mesh(cube3, material3);
      mesh3.position.x = -0.45;
      mesh3.position.y = 0;
      mesh3.position.z = 0;
      mesh3.updateMatrix();
      mesh3.name = 'cube3'
      this.scene.add(mesh3); 

    },

    // 射线获取对象

    onMouseClick() {
        //声明raycaster和mouse变量
        this.raycaster = new THREE.Raycaster();
        this.mouse = new THREE.Vector2();

        //通过鼠标点击的位置计算出raycaster所需要的点的位置，以屏幕中心为原点，值的范围为-1到1.

        this.mouse.x = ( window.event.touches[0].clientX / window.innerWidth ) * 2 - 1;
        this.mouse.y = - ( window.event.touches[0].clientY / window.innerHeight ) * 2 + 1;

        // 通过鼠标点的位置和当前相机的矩阵计算出raycaster
        this.raycaster.setFromCamera( this.mouse, this.camera );

        // 获取raycaster直线和所有模型相交的数组集合
        const intersects = this.raycaster.intersectObjects( this.scene.children, true );
        console.log(intersects)
        // console.log(window.event.touches[0].clientX)
        // intersects[0].object.material.color.set( 0xff0000 )

        if(intersects[0].object.name == 'cube1'){
          console.log(1)
          this.$emit('photoShow', true, 'cube1')
        }

        if(intersects[0].object.name == 'cube2'){
          this.scene2 = new THREE.Scene();
          const textures = this.getTexturesFromAtlasFile("/static/qccolor2.jpg", 6);

          const materials = [];

          for (let i = 0; i < 6; i++) {
            materials.push(new THREE.MeshBasicMaterial({ map: textures[i] }));
          }

          const skyBox = new THREE.Mesh(new THREE.BoxGeometry(1, 1, 1), materials);
          skyBox.geometry.scale(1, 1, -1);
          skyBox.geometry.translate(0, 0, 0);
          this.scene2.add(skyBox);
          this.scene = this.scene2

        }

        if(intersects[0].object.name == 'cube3'){
          console.log(3)
          this.$emit('photoShow', true, 'cube3')
        }

        if(intersects[0].object.name == 'cat'){
          console.log('cat')
          // this.$emit('photoShow', true, 'cube3')
        }


        
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
