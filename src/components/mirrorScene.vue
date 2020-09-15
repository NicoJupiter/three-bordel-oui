<template>
    <div class="mirror-scene" ref="mirrorScene">

    </div>
</template>


<script>
    import * as THREE from 'three'
    //import { Reflector } from 'three/examples/jsm/objects/Reflector';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    import Stats from 'three/examples/jsm/libs/stats.module.js';

    export default {
        name: 'mirrorScene',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                sprite: null,
                particles: null,
                numberEyes:5,
                raycaster:null,
                isFinished: false,
                eyeSprite:null,
                group:null
            }
        },

        methods : {
            init() {
                this.scene = new THREE.Scene();
                this.scene.background = new THREE.Color(0x000000);

                this.camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
                this.camera.position.set(0,0,1);

                let ambient = new THREE.HemisphereLight(0xffb8c6, 0x080820);
                this.scene.add(ambient);

                let light = new THREE.DirectionalLight(0xffffff, 1);
                light.position.set( 1, 10, 6);
                this.scene.add(light);

                this.stats = new Stats();
                document.body.appendChild( this.stats.dom );

                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                document.body.appendChild( this.renderer.domElement );

                let controls = new OrbitControls( this.camera, this.renderer.domElement );
                controls.target.set(0,0,0);
                controls.update();

                this.raycaster = new THREE.Raycaster();

                this.group = new THREE.Group();

                var spriteMap = new THREE.TextureLoader().load( "oeil.png" );
                var spriteMaterial = new THREE.SpriteMaterial( { map: spriteMap } );
                this.eyeSprite = new THREE.Sprite( spriteMaterial );
                this.eyeSprite.scale.set(5, 5, 1);


                this.generateEye(this.numberEyes);

                window.addEventListener( 'resize', this.resize, false);
                window.addEventListener('keypress', this.eyesAttraction);
                window.addEventListener('click', this.shootEye);
            },
            generateEye(number) {

                for(var i = 0; i < number; i++) {
                    var x = THREE.MathUtils.randFloatSpread( 50 );
                    var y = THREE.MathUtils.randFloatSpread( 50 );
                    var z = THREE.MathUtils.randFloatSpread( 50 );

                    var clonedSprite = this.eyeSprite.clone();
                    clonedSprite.position.set(x,y,z);
                    this.group.add(clonedSprite);

                    this.scene.add( this.group );
                }

            },
            eyesAttraction() {

                let eyesAttractionFrame = requestAnimationFrame(this.eyesAttraction);

                this.group.children.forEach((child) => {
                    var position = child.position;

                    var numberx = position.x + (0.0 - position.x) * 0.01;
                    var numbery = position.y + (0.0 - position.y) * 0.01;
                    var numberz = position.z + (0.0 - position.z) * 0.01;

                    var distX = Math.round(numberx * 1000) / 1000;
                    var distY = Math.round(numbery * 1000) / 1000;
                    var distZ = Math.round(numberz * 1000) / 1000;

                    if(distX > -.1 && distY > -.1 && distZ > -.1) {
                        cancelAnimationFrame(eyesAttractionFrame);
                    } else {
                        position.x = distX;
                        position.y = distY;
                        position.z = distZ;
                    }

                })

            },
            shootEye(event) {
                if(!this.isFinished) {
                    console.log('click');
                    let mouse = new THREE.Vector2();
                    mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
                    mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;
                    this.raycaster.setFromCamera(mouse, this.camera);
                    let intersects = this.raycaster.intersectObjects(this.group.children);

                    if (intersects.length > 0) {
                        this.group.remove(intersects[0].object);
                        this.generateEye(3);
                    }
                }
            },
            update() {
                requestAnimationFrame( this.update );
                this.renderer.render( this.scene, this.camera );
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