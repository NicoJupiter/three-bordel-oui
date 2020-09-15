<template>
    <div class="sound-scene" ref="soundScene">
        <div class="buttons">
            <a href="#" id="firstOst">First Ost</a>
            <a href="#" id="secondOst">Second Ost</a>
            <a href="#" id="thirdOst">Third Ost</a>
        </div>
    </div>
</template>

<script>
    import * as THREE from 'three'
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
    //import { GUI } from 'three/examples/jsm/libs/dat.gui.module.js';
    //import { PositionalAudioHelper } from 'three/examples/jsm/helpers/PositionalAudioHelper.js';

    export default {
        name: 'soundScene',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                control: null,
                sounds: [],
                soundControl: null
            }
        },
        methods : {
            init() {

                this.scene = new THREE.Scene();
                let color = 0xffb8c6;
                this.scene.background = new THREE.Color(color);

                this.camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 1000);
                this.camera.position.set(0,50,1)

                let ambient = new THREE.HemisphereLight(0xffb8c6, 0x080820);
                this.scene.add(ambient);

                this.renderer = new THREE.WebGLRenderer();
                this.renderer.setSize( window.innerWidth, window.innerHeight );
                this.renderer.outputEncoding = THREE.sRGBEncoding;
                document.body.appendChild( this.renderer.domElement );

                this.loadSound();
                this.setPositionalAudio();

                this.controls = new OrbitControls( this.camera, this.renderer.domElement );
                this.controls.target.set(0,0,0);
                this.controls.update();

                let firstButton = document.getElementById('firstOst');

                firstButton.addEventListener('click', (e) => {
                    e.preventDefault();

                    this.sounds[0].play();
                    this.sounds[1].pause();
                    this.sounds[2].pause();
                });

                let secondButton = document.getElementById('secondOst');

                secondButton.addEventListener('click', (e) => {
                    e.preventDefault();

                    this.sounds[0].pause();
                    this.sounds[1].play();
                    this.sounds[2].pause();
                });

                let thirdButton = document.getElementById('thirdOst');

                thirdButton.addEventListener('click', (e) => {
                    e.preventDefault();

                    this.sounds[0].pause();
                    this.sounds[1].pause();
                    this.sounds[2].play();
                });

                //scene plate
                let planeGeometry = new THREE.PlaneGeometry(200, 200);
                let planeMaterial = new THREE.MeshStandardMaterial();
                let plane = new THREE.Mesh(planeGeometry, planeMaterial);
                plane.rotation.x = -Math.PI/2;
                this.scene.add(plane);

                //add grid on scene
                let grid = new THREE.GridHelper(200, 80);
                this.scene.add( grid );

            },

            loadSound() {
                let audioListener = new THREE.AudioListener();
                this.camera.add( audioListener );

                let firstOst = new THREE.Audio(audioListener);
                let secondOst = new THREE.Audio(audioListener);
                let thirdOst = new THREE.Audio(audioListener);

                this.scene.add(firstOst);
                this.scene.add(secondOst);
                this.scene.add(thirdOst);

                var loader = new THREE.AudioLoader();

                loader.load(
                    'musicScene2.mp3',

                    // onLoad callback
                    function ( audioBuffer ) {

                        firstOst.setBuffer( audioBuffer );
                        firstOst.setLoop( true );
                        firstOst.setVolume( 0.5 );
                        console.log(firstOst);
                    },

                    function ( xhr ) {
                        console.log( (xhr.loaded / xhr.total * 100) + '% loaded' );
                    },

                    function ( err ) {
                        console.log( err );
                    }
                );

                loader.load(
                    'ost2.mp3',

                    // onLoad callback
                    function ( audioBuffer ) {
                        secondOst.setBuffer( audioBuffer );
                        secondOst.setLoop( true );
                        secondOst.setVolume( 0.5 );
                    },

                    function ( xhr ) {
                        console.log( (xhr.loaded / xhr.total * 100) + '% loaded' );
                    },

                    function ( err ) {
                        console.log( err );
                    }
                );

                loader.load(
                    'ost3.mp3',

                    // onLoad callback
                    function ( audioBuffer ) {
                        thirdOst.setBuffer( audioBuffer );
                        thirdOst.setLoop( true );
                        thirdOst.setVolume( 0.5 );
                    },

                    function ( xhr ) {
                        console.log( (xhr.loaded / xhr.total * 100) + '% loaded' );
                    },

                    function ( err ) {
                        console.log( err );
                    }
                );

                this.sounds.push(firstOst);
                this.sounds.push(secondOst);
                this.sounds.push(thirdOst);

            },

            setPositionalAudio() {

                var listener = new THREE.AudioListener();
                this.camera.add( listener );

                let sphere = new THREE.SphereBufferGeometry( 20, 32, 16 );
                let material = new THREE.MeshPhongMaterial( { color: 0xffaa00, flatShading: true, shininess: 0 } );

                let audioLoader = new THREE.AudioLoader();

                let mesh1 = new THREE.Mesh( sphere, material );
                mesh1.position.set( - 250, 30, 0 );
                this.scene.add( mesh1 );

                var sound1 = new THREE.PositionalAudio( listener );
                audioLoader.load('ost.mp3', function ( buffer ) {

                    sound1.setBuffer( buffer );
                    sound1.setRefDistance( 1 );
                    sound1.setLoop(true)
                    sound1.play();
                   /* let helperAudio = new PositionalAudioHelper(sound1, 0.1);
                    sound1.add( helperAudio );*/
                });
                mesh1.add( sound1 );


            },

            update() {
                requestAnimationFrame(this.update);
                if(this.controls !== null) {
                    this.controls.update();
                }
                this.renderer.render(this.scene, this.camera)
            }
        },
        mounted() {
            this.init();
            this.update();
        }
    }
</script>

<style>
    .buttons {
        position: absolute;
        top: 5%;
        left: 5%;
    }
    .buttons a {
        text-decoration: none;
        background-color: deeppink;
        color: white;
        padding: 15px;
    }

    .buttons a:not(:last-child) {
        margin-right: 5px;
    }
</style>