<template>
    <div class="blurEffectScene">

    </div>
</template>

<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    import { GUI } from 'three/examples/jsm/libs/dat.gui.module.js';
    import { AfterimagePass } from 'three/examples/jsm/postprocessing/AfterimagePass.js';
    import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer.js';
    import { RenderPass } from 'three/examples/jsm/postprocessing/RenderPass.js';

    export default {
        name: "blurEffectScene",
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                composer: null,
                afterimagePass:null,
                params: {
                    enable:true
                }
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
                light.position.set( 0,20, 50);
                this.scene.add(light);

                let ambient = new THREE.HemisphereLight(0xffb8c6, 0x080820);
                this.scene.add(ambient);

                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                this.renderer.outputEncoding = THREE.sRGBEncoding;
                document.body.appendChild( this.renderer.domElement );


                let controls = new OrbitControls( this.camera, this.renderer.domElement );
                controls.target.set(0,0,0);
                controls.update();

                let helper = new THREE.GridHelper( 1000, 10, 0x444444, 0x444444 );
                helper.position.y = 0.1;
                this.scene.add( helper );

                let sphere = new THREE.SphereBufferGeometry( 20, 32, 16 );
                let material = new THREE.MeshPhongMaterial( { color: 0xffaa00, flatShading: true, shininess: 0 } );

                let mesh1 = new THREE.Mesh( sphere, material );
                mesh1.position.set( - 250, 30, 0 );
                this.scene.add( mesh1 );

                this.composer = new EffectComposer(this.renderer);
                this.composer.addPass(new RenderPass(this.scene, this.camera));

                this.afterimagePass = new AfterimagePass();
                this.composer.addPass( this.afterimagePass );
            },
            createGUI() {

                let gui = new GUI( { name: 'Damp setting' } );
                gui.add( this.afterimagePass.uniforms[ "damp" ], 'value', 0, 1 ).step( 0.001 );
                gui.add( this.params, 'enable' );

            },
            update() {
                requestAnimationFrame( this.update );
                if(this.params.enable) {
                    this.composer.render();
                } else {
                    this.renderer.render( this.scene, this.camera );
                }

            },
        },
        mounted() {
            this.init();
            this.createGUI();
            this.update();
        }
    }
</script>

<style scoped>

</style>