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
      mouse: null,
      group: null,
      group2:null,
      clock: null,
      clock2: null,
      mixer: null,
      mixer2: null
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

      // 创建一个帧动画时钟对象Clock
      this.clock = new THREE.Clock();
      this.clock2 = new THREE.Clock();
      // 创建一个帧动画组
      this.group = new THREE.Group();
      this.group2 = new THREE.Group();



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
      this.scene.add(mesh1);  

      // 测试添加正方体2(环境变化)
      // const cube2 = new THREE.BoxGeometry(0.03, 0.03, 0.03);
      // const material2 = new THREE.MeshBasicMaterial({color:0x00ff00})
      // const mesh2 = new THREE.Mesh(cube2, material2);
      // mesh2.position.x = 0.45;
      // mesh2.position.y = -0.1;
      // mesh2.position.z = 0;
      // mesh2.updateMatrix();
      // mesh2.name = 'cube2'
      // this.scene.add(mesh2); 


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


      // 测试帧动画正方体4
      const cube4 = new THREE.BoxGeometry(0.03, 0.03, 0.03);
      const material4 = new THREE.MeshBasicMaterial({color:0xFF00FF})
      const mesh4 = new THREE.Mesh(cube4, material4);
      mesh4.position.x = 0.45;
      mesh4.position.y = -0.1;
      mesh4.position.z = 0;
      mesh4.updateMatrix();
      mesh4.name = 'cube4' ;
      this.group.add(mesh4);
      this.scene.add(this.group); 


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
          // cat.children[0].name = 'cat';
          cat.name = 'cat';
          that.group2.add(cat);
          that.scene.add(that.group2);  
        })
      })

      // 测试飞船模型
      // let objloader2=new OBJLoader();
      // let mtlloader2=new MTLLoader();
      // mtlloader2.load('/static/feichuan.mtl', function(materials) {
      //   // 返回一个包含材质的对象MaterialCreator
      //   // console.log(materials);
      //   //obj的模型会和MaterialCreator包含的材质对应起来
      //   objloader2.setMaterials(materials);
      //   objloader2.load('/static/feichuan.obj', function(obj) {
      //     let feichuan=obj;
      //     feichuan.scale.set(0.0001,0.0001,0.0001)
      //     feichuan.children[0].name = 'feichuan'
      //     that.scene.add(feichuan);
      //   })
      // })


      /**
       * 编辑group子对象网格模型mesh1和mesh2的帧动画数据
       */
      // 创建名为Box对象的关键帧数据
      var times = [0, 20]; //关键帧时间数组，离散的时间点序列
      var values = [0.35, -0.2, 0, 0.45, -0.1, 0]; //与时间点对应的值组成的数组
      // 创建位置关键帧对象：0时刻对应位置0, 0, 0   20时刻对应位置150, 0, 0
      var posTrack = new THREE.KeyframeTrack('cube4.position', times, values);
      // 创建名为Sphere对象的关键帧数据  从0~20时间段，尺寸scale缩放3倍
      var scaleTrack = new THREE.KeyframeTrack('cat.scale', [0, 20], [1, 1, 1, 2, 2, 2]);

      // duration决定了默认的播放时间，一般取所有帧动画的最大时间
      // duration偏小，帧动画数据无法播放完，偏大，播放完帧动画会继续空播放
      var duration = 20;
      // 多个帧动画作为元素创建一个剪辑clip对象，命名"default"，持续时间10
      var clip = new THREE.AnimationClip("default", duration, [posTrack]);
      var clip2 = new THREE.AnimationClip("default", duration, [scaleTrack]);


      /**
       * 播放编辑好的关键帧数据
       */
      // group作为混合器的参数，可以播放group中所有子对象的帧动画
      this.mixer = new THREE.AnimationMixer(this.group);
      this.mixer2 = new THREE.AnimationMixer(this.group2);
      // 剪辑clip作为参数，通过混合器clipAction方法返回一个操作对象AnimationAction
      var AnimationAction = this.mixer.clipAction(clip);
      var AnimationAction2 = this.mixer2.clipAction(clip2);
      //通过操作Action设置播放方式
      AnimationAction.timeScale = 20;//默认1，可以调节播放速度
      AnimationAction2.timeScale = 20;
      // AnimationAction.loop = THREE.LoopOnce; //不循环播放
      AnimationAction.play();//开始播放
      AnimationAction2.play();

    },


    // 射线获取对象，并根据点击的对象触发对应的事件

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

        if(intersects[0].object.name == 'cube4'){
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

        if(intersects[0].object.parent.name == 'cat'){
          console.log('cat')
          // this.$emit('photoShow', true, 'cube3')
        }


        
    },



    // 环境贴图加载创建
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

    // 窗口大小变化则再次渲染
    onWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();

      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },

    // 画面渲染
    animate() {
      requestAnimationFrame(this.animate);

      this.controls.update(); // required when damping is enabled

      this.renderer.render(this.scene, this.camera);

      //clock.getDelta()方法获得两帧的时间间隔
      // 更新混合器相关的时间
      this.mixer.update(this.clock.getDelta());
      this.mixer2.update(this.clock2.getDelta());
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
