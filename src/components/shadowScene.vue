<template>
    <div class="shadowScene">

    </div>
</template>

<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    export default {
        name: "shadowScene",
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
                let color = 0xffb8c6;
                this.scene.background = new THREE.Color(color);

                this.camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 2000);
                this.camera.position.set( 0,50, 1);

                let light = new THREE.DirectionalLight(0xffffff, 1);
                light.castShadow = true;
                light.position.set( 0,50, 0);
                this.scene.add(light);

                let ambient = new THREE.HemisphereLight(0xffb8c6, 0x080820);
                this.scene.add(ambient);

                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                this.renderer.outputEncoding = THREE.sRGBEncoding;
                this.renderer.shadowMap.enabled = true;
                this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;

                document.body.appendChild( this.renderer.domElement );

                let controls = new OrbitControls( this.camera, this.renderer.domElement );
                controls.target.set(0,0,0);
                controls.update();

                //Create a sphere that cast shadows (but does not receive them)
                var sphereGeometry = new THREE.SphereBufferGeometry( 3, 10, 10 );
                var sphereMaterial = new THREE.MeshStandardMaterial( { color: 0xff0000 } );
                var sphere = new THREE.Mesh( sphereGeometry, sphereMaterial );
                sphere.position.set(0, 15, 0);
                sphere.castShadow = true; //default is false
                sphere.receiveShadow = false; //default
                this.scene.add( sphere );


                var boxGeometry = new THREE.BoxGeometry(8,8, 8);
                var boxMaterial = new THREE.MeshStandardMaterial( { color: 0xff0000 } );
                var box = new THREE.Mesh(boxGeometry, boxMaterial);
                box.position.set(0, 5, 0);
                box.castShadow = true; //default is false
                box.receiveShadow = true; //default
                this.scene.add( box );

                //Create a plane that receives shadows (but does not cast them)
                var planeGeometry = new THREE.PlaneBufferGeometry( 20, 20, 32, 32 );
                var planeMaterial = new THREE.MeshStandardMaterial( { color: 0xff4d61 } )
                var plane = new THREE.Mesh( planeGeometry, planeMaterial );
                plane.receiveShadow = true;
                plane.rotation.x = -Math.PI/2;
                this.scene.add( plane );

            },
            update() {
                requestAnimationFrame(this.update);
                this.renderer.render(this.scene, this.camera)
            }
        },
        mounted() {
            this.init();
            this.update();
        }
    }
</script>

<style scoped>

</style>