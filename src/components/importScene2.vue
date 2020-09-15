<template>
    <div id="container">

    </div>
</template>

<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
    export default {
        name: 'importScene2',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
            }
        },
        methods: {
            init() {
                this.scene = new THREE.Scene();
                this.scene.background = new THREE.Color('black');

                this.camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 3000);
                this.camera.position.set( 0,100, -500);

                let spotLight = new THREE.SpotLight( 0xff3bfc );
                spotLight.position.set(0, 100, -550);

                spotLight.castShadow = true;

                spotLight.shadow.mapSize.width = 1024;
                spotLight.shadow.mapSize.height = 1024;

                spotLight.shadow.camera.near = 500;
                spotLight.shadow.camera.far = 4000;
                spotLight.shadow.camera.fov = 30;
                this.scene.add( spotLight );


                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                this.renderer.outputEncoding = THREE.sRGBEncoding;
                document.body.appendChild( this.renderer.domElement );

                let controls = new OrbitControls( this.camera, this.renderer.domElement );
                controls.target.set(0,0,0);
                controls.update();

                let loader = new GLTFLoader();
                let self = this;

                loader.load('MaleGaze_SCENES032.glb', function (object) {
                    console.log(object);
                   object.scene.traverse(function (child) {
                       if (child.isMesh){
                          // child.material.envMap = envMap;
                           child.castShadow = true;
                           child.receiveShadow = true;
                       }
                   });

                   self.scene.add(object.scene);
               });

                window.addEventListener( 'resize', this.resize, false);
            }
            ,

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
            this.update();
        }
    }
</script>

<style>
    html {
        margin: 0;
        padding: 0;
    }
</style>