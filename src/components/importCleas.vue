<template>
    <div class="import-cleas" ref="importCleas">

    </div>
</template>

<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader.js';
    import { MTLLoader } from 'three/examples/jsm/loaders/MTLLoader';

    export default {
        name: 'importCleas',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                control: null,
                obj: null
            }
        },
        methods: {
            init() {
                this.scene = new THREE.Scene();
                let color = 0xffb8c6;
                this.scene.background = new THREE.Color(color);

                this.camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 1000);
                this.camera.position.z = 3;

                var backLight = new THREE.DirectionalLight(0xffffff);
                backLight.position.set(0, 0, -110).normalize();

                var helper = new THREE.DirectionalLightHelper( backLight, 10 );
                this.scene.add( helper );

                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                document.body.appendChild( this.renderer.domElement );

                var ambient = new THREE.AmbientLight(0xffff00, 1.0);
                this.scene.add(ambient);

                var objLoader = new OBJLoader();
                var mtlLoader = new MTLLoader();

                mtlLoader.load('echelle.mtl', (materials) => {

                    console.log(materials);
                    materials.preload();


                    objLoader.setMaterials(materials);

                    objLoader.load('echelle.obj', (object) => {
                        this.obj = object;
                        this.obj.position.z = -50;
                        this.scene.add(this.obj);
                    });

                });


                this.control = new OrbitControls(this.camera, this.renderer.domElement);
                //this.controls.enableDamping = true;
               /* this.controls.dampingFactor = 0.25;
                this.controls.enableZoom = false;*/

            },
            update() {
                requestAnimationFrame(this.update);
                if(this.obj !== null) {
                    this.obj.rotation.y += .01
                }
                this.control.update();
                this.renderer.render(this.scene, this.camera)
            }
        },
        mounted() {
            this.init();
            this.update();
        }
    }
</script>