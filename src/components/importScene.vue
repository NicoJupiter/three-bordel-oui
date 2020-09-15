<template>
    <div class="import-scene" ref="importScene">

    </div>
</template>


<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
   /* import FBXloader from 'three-fbx-loader'*/
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';

    export default {
        name: 'importScene',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                manObject: null
            }
        },

        methods : {
            init() {

                this.scene = new THREE.Scene();

                this.camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 2000);
                this.camera.position.set( 50,-150, -300);

                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                this.renderer.outputEncoding = THREE.sRGBEncoding;
                this.renderer.shadowMap.enabled = true;
               this.renderer.shadowMap.autoUpdate = false;
               this.renderer.shadowMap.needsUpdate = true;
                this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;
                document.body.appendChild( this.renderer.domElement );

                let controls = new OrbitControls( this.camera, this.renderer.domElement );
                controls.target.set(0,0,0);
                controls.update();


                window.addEventListener( 'resize', this.resize, false);
            },
            buildLight() {

                //Light 1
              /* var spotLight = new THREE.SpotLight( 0xFFE5BF, .4 );
              spotLight.position.set( 200, 400, -550 );
              this.scene.add(spotLight);

              var spotLightHelper = new THREE.SpotLightHelper(spotLight)
              this.scene.add(spotLightHelper);*/

              //Light 4
                let pointLight = new THREE.PointLight(0xFFE5A3, .7, 500);
                pointLight.position.set(150,150, -350);
                pointLight.castShadow = true;
                this.scene.add(pointLight);

                var pointLightHelper = new THREE.PointLightHelper(pointLight, 10)
                this.scene.add(pointLightHelper)

                //Light 5
                var directionnalLight =  new THREE.DirectionalLight( 0xffffff, 0.5 );
                directionnalLight.position.set(-100,150, -0);
                directionnalLight.target = this.manObject;
                this.scene.add(directionnalLight);

                var directionnalLightHelper = new THREE.DirectionalLightHelper( directionnalLight, 5 );
                this.scene.add( directionnalLightHelper );

                //Light 7
                var directionnalLight2 =  new THREE.DirectionalLight( 0xC96934, .48 );
                directionnalLight2.position.set(-100,150, 80);
                directionnalLight2.target = this.manObject;
                this.scene.add(directionnalLight2);

                var directionnalLightHelper2 = new THREE.DirectionalLightHelper( directionnalLight2, 5 );
                this.scene.add( directionnalLightHelper2 );

                //Light 9
                var directionnalLight3 =  new THREE.DirectionalLight( 0x68AB54, .48 );
                directionnalLight3.position.set(10,350, 200);
                directionnalLight3.target = this.manObject;
                this.scene.add(directionnalLight3);

                var directionnalLightHelper3 = new THREE.DirectionalLightHelper( directionnalLight3, 5 );
                this.scene.add( directionnalLightHelper3 );


            },
            importScene() {
                let loader = new GLTFLoader();

                //actress_scenery

                this.manObject = new THREE.Object3D();
                loader.load('cameraman_scenery_fail.glb', (object) => {
                    object.scene.traverse((child) => {

                        child.castShadow = true;
                        child.receiveShadow = true;
                        if (child.name === "DENNIS"){
                            this.manObject = child;

                        }
                    });
                    this.scene.add(this.manObject);
                    this.scene.add(object.scene);
                });

                loader.load('actress_scenery.glb', (object) => {

                    object.scene.traverse((child) => {

                        child.castShadow = false;
                        child.receiveShadow = true;

                    });
                    this.scene.add(object.scene);
                });
            },
            update() {
                requestAnimationFrame( this.update );
                this.renderer.render( this.scene, this.camera );
            },

            resize() {
                this.camera.aspect = window.innerWidth / window.innerHeight;
                this.camera.updateProjectionMatrix();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
            }
        },
        mounted() {
            this.init();
            this.importScene();
            setTimeout(() => {
                this.buildLight();
            },1000)

            this.update();
        }
    }
</script>

<style>
    body {
        margin: 0;
        padding: 0;
    }
</style>