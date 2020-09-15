<template>
    <div id="raycaster-scene" class="raycaster" ref="raycasterScene">
        <div class="raycaster__sight">
        </div>
        <div class="progress">
            <div class="progress-bar" role="progressbar" v-bind:aria-valuenow="compter" aria-valuemin="0" aria-valuemax="100"
                 :style="{ width: (compter / 500) * 100 + '%' }">
            </div>
        </div>
    </div>
</template>

<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    export default {
        name: 'raycasterScene',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                raycaster: null,
                compter: 0
            }
        },
        methods: {
            init() {
                this.scene = new THREE.Scene();
                let color = 0xffb8c6;
                this.scene.background = new THREE.Color(color);
                this.scene.fog = new THREE.Fog(color, 10, 100);

                this.camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
                this.camera.position.set(0,5,5);

                this.raycaster = new THREE.Raycaster();

                let ambient = new THREE.HemisphereLight(0xffb8c6, 0x080820);
                this.scene.add(ambient);

                let light = new THREE.DirectionalLight(0xffffff, 1);
                light.position.set( 1, 10, 6);
                this.scene.add(light);

                let container = document.getElementById('raycaster-scene');

                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                container.appendChild( this.renderer.domElement );

                let controls = new OrbitControls( this.camera, this.renderer.domElement );
                controls.target.set(0,0,0);
                controls.update();

                this.buildEye()

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
            },

            buildEye() {

                this.scene.remove(this.particles);
                var geometry = new THREE.BufferGeometry();
                let vertices = [];

                this.sprite = new THREE.TextureLoader().load( 'oeil.png' );

                for ( var i = 0; i < 5; i ++ ) {

                    var x = THREE.MathUtils.randFloatSpread( 50 );
                    var y = THREE.MathUtils.randFloatSpread( 50 );
                    var z = THREE.MathUtils.randFloatSpread( 50 );

                    vertices.push(x, y, z);

                }

                geometry.setAttribute( 'position', new THREE.Float32BufferAttribute( vertices, 3 ) );

                let material = new THREE.PointsMaterial( {map: this.sprite, alphaTest: 0.5, size: 5.0} );

                this.particles = new THREE.Points( geometry, material );
                this.scene.add( this.particles );


            },

            raycast() {

                requestAnimationFrame(this.raycast);
                this.raycaster.setFromCamera({x: 0.0, y: 0.0}, this.camera);
                // calculate objects intersecting the picking ray
                let intersects = this.raycaster.intersectObjects( this.scene.children );

                if(intersects.length > 0) {
                    if(this.compter <= 500) {
                        this.compter++;
                    } else {
                        this.compter = 0;
                    }
                }
            }
        },
        mounted() {
            this.init();
            this.update();
            this.raycast();
        }
    }
</script>

<style>
    .raycaster {
        width: 100%;
        height: 100%;
    }
    .raycaster__sight {
        width: 5px;
        height: 25px;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        background-color: red;
    }

    .raycaster__sight::after {
        content: '';
        width: 25px;
        height: 5px;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        background-color: red;
    }

    .raycaster__progressBar {
        position: absolute;
        top: 5%;
        left: 5%;
    }

    .progress {
        width: 150px;
        height: 25px;
        position: absolute;
        top: 5%;
        left: 5%;
        background-color: white;
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.25) inset;
        border-radius: 2px;
    }

    .progress-bar {
        height: 25px;
        background-color: hotpink;
    }

</style>