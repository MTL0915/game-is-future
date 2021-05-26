<template>
  <div id="env"></div>
</template>

<script>

import * as THREE from '../../node_modules/three/build/three.module.js';

import Stats from '../../node_modules/three/examples/jsm/libs/stats.module.js';

import { GUI } from '../../node_modules/three/examples/jsm/libs/dat.gui.module.js';
import { TrackballControls } from '../../node_modules/three/examples/jsm/controls/TrackballControls.js';
import { OBJLoader } from '../../node_modules/three/examples/jsm/loaders/OBJLoader.js';
import { RGBELoader } from '../../node_modules/three/examples/jsm/loaders/RGBELoader.js';

export default {
  name: "Env",
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      controls: null,
      container:null, 
      stats:null,
      statsEnabled:true
    };
  },
  methods: {
    // 初始化
			init() {

				this.container = document.getElementById('env')
				// document.getElementById('env').appendChild( this.container );

				this.renderer = new THREE.WebGLRenderer( { antialias: true } );
				this.renderer.setPixelRatio( window.devicePixelRatio );
				this.renderer.setSize( window.innerWidth, window.innerHeight );
				this.container.appendChild( this.renderer.domElement );

				this.renderer.outputEncoding = THREE.sRGBEncoding;
				this.renderer.toneMapping = THREE.ReinhardToneMapping;
				this.renderer.toneMappingExposure = 3;

				//

				this.scene = new THREE.Scene();

				this.camera = new THREE.PerspectiveCamera( 50, window.innerWidth / window.innerHeight, 10, 1000);
				this.camera.position.x = 200;
				this.camera.position.z = 500;
				this.camera.position.y = 200;

				this.controls = new TrackballControls( this.camera, this.renderer.domElement );

				this.controls.noRotate = false; // 旋转
				this.controls.noZoom = true; // 滚动缩放
				this.controls.noPan = true; // 右键平移 
				this.controls.minDistance = 300; //最近距离
				this.controls.maxDistance = 800; //最远距离

				//

				this.scene.add( new THREE.HemisphereLight( 0x443333, 0x222233, 4 ) );

				//

				const material = new THREE.MeshStandardMaterial();

        let that=this;       

				new OBJLoader()
					.setPath( '/static/' )
					// .load( 'Cerberus.obj', function ( group ) {
					.load( 'feichuan.obj', function ( group ) {
						const loader = new THREE.TextureLoader()
							.setPath( '/static/' );

						material.roughness = 1; // attenuates roughnessMap
						material.metalness = 1; // attenuates metalnessMap

						const diffuseMap = loader.load( 'VRayMtl1SG_Base_Color.png' );
						diffuseMap.encoding = THREE.sRGBEncoding;
						material.map = diffuseMap;
						// roughness is in G channel, metalness is in B channel
						material.metalnessMap = material.roughnessMap = loader.load( 'VRayMtl1SG_Emissive.png' );
						material.normalMap = loader.load( 'VRayMtl1SG_Normal_OpenGL.png' );

						material.map.wrapS = THREE.RepeatWrapping;
						material.roughnessMap.wrapS = THREE.RepeatWrapping;
						material.metalnessMap.wrapS = THREE.RepeatWrapping;
						material.normalMap.wrapS = THREE.RepeatWrapping;

						group.traverse( function ( child ) {

							if ( child.isMesh ) {

								child.material = material;

							}

						} );

						group.position.x = - 0.45;
						group.rotation.y = - Math.PI / 2;
						that.scene.add( group );

					} );

				const environments = {

					'Venice Sunset': { filename: 'quchuan.hdr' },
					'Overpass': { filename: 'pedestrian_overpass_1k.hdr' }

				};

				function loadEnvironment(name) {
          
					if ( environments[ name ].texture !== undefined ) {

						that.scene.background = environments[ name ].texture;
						that.scene.environment = environments[ name ].texture;
						return;

					}

					const filename = environments[ name ].filename;
					new RGBELoader()
					.setDataType( THREE.UnsignedByteType )
					.setPath( '/static/' )
					.load( filename, function ( hdrEquirect ) {

						const hdrCubeRenderTarget = pmremGenerator.fromEquirectangular( hdrEquirect );
						hdrEquirect.dispose();

						that.scene.background = hdrCubeRenderTarget.texture;
						that.scene.environment = hdrCubeRenderTarget.texture;
						environments[ name ].texture = hdrCubeRenderTarget.texture;

					} );

				}

				const params = {

					environment: Object.keys( environments )[ 0 ]

				};
				loadEnvironment( params.environment );

				const gui = new GUI();
				gui.add( params, 'environment', Object.keys( environments ) ).onChange( function( value ) {

					loadEnvironment(value);

				} );
				gui.open();

				const pmremGenerator = new THREE.PMREMGenerator( this.renderer );
				pmremGenerator.compileEquirectangularShader();

				//

				if ( this.statsEnabled ) {

					this.stats = new Stats();
					this.container.appendChild( this.stats.dom );

				}

				//监听浏览器窗户页面宽高的变化
				window.addEventListener( 'resize', this.onWindowResize );

			},

      onWindowResize() {

				this.renderer.setSize( window.innerWidth, window.innerHeight );

				this.camera.aspect = window.innerWidth / window.innerHeight;
				this.camera.updateProjectionMatrix();

			},

      animate() {

				requestAnimationFrame( this.animate );

				this.controls.update();
				this.renderer.render( this.scene, this.camera );

				if ( this.statsEnabled ) this.stats.update();

			}


  },
  mounted() {
    this.init();
    this.animate()
  },
};
</script>



