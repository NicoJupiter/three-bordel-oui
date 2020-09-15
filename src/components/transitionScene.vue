<template>
    <div id="container"></div>
</template>


<script>
    import * as THREE from 'three';

    export default {
        name: 'transitionScene',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                container: null
            }
        },

        methods: {
            init() {
                this.container = document.getElementById("container");
                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                this.container.appendChild(this.renderer.domElement);

               // this.sceneExemple();
            },

            sceneExemple(color) {
                this.scene = new THREE.Scene();
                this.scene.background = new THREE.Color(color);
                this.scene.fog = new THREE.Fog(color, 10, 100);

                this.camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
                this.camera.position.set(0,5,12);

                let ambient = new THREE.HemisphereLight(0xffb8c6, 0x080820);
                this.scene.add(ambient);

                let light = new THREE.DirectionalLight(0xffffff, 1);
                light.position.set( 1, 10, 6);
                this.scene.add(light);
            }
        }
    }
</script>