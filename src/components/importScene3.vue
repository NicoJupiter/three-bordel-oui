<template>
    <div id="container">

    </div>
</template>

<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
    export default {
        name: 'importScene3',
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
                this.camera.position.set(0, 200, 1);

               let light2 = new THREE.DirectionalLight(0xffffff, 1);
                light2.position.set( 0,20, 50);
                this.scene.add(light2);

                let ambient = new THREE.HemisphereLight(0xffb8c6, 0x080820);
                this.scene.add(ambient);


                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                document.body.appendChild( this.renderer.domElement );

                let controls = new OrbitControls( this.camera, this.renderer.domElement );
                controls.target.set(0,0,0);
                controls.update();

                let loader = new GLTFLoader();

                loader.load('Scene-01-cellule.gltf', (object) => {
                    object.scene.traverse(function (child) {
                      console.log(child);
                    });
                    this.scene.add(object.scene);
                });

                window.addEventListener( 'resize', this.resize, false);
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