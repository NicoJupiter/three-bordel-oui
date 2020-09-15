<template>
    <div class="imageEffect">
        <a class="link" href="#">
           <!-- <img src="img/demo1/img1.jpg" alt="Some image" />-->
        </a>
    </div>
</template>

<script>
    import * as THREE from 'three'
    export default {
        name: "imageEffect",
        data() {
            return {
                effectSchell : {

                    constructor(container = document.body, itemsWrapper = null) {
                        this.container = container;
                        this.itemsWrapper = itemsWrapper;
                        if (!this.container || !this.itemsWrapper) return;
                        this.setup()
                    },

                    get itemsElements() {
                        // convert NodeList to Array
                        const items = [...this.itemsWrapper.querySelectorAll('.link')]

                        //create Array of items including element, image and index
                        return items.map((item, index) => ({
                            element: item,
                            img: item.querySelector('img') || null,
                            index: index
                        }))
                    }
                }
            }

        },
        methods: {
            setup() {
                window.addEventListener('resize', this.onWindowResize.bind(this), false)


                // renderer
                this.renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
                this.renderer.setSize(this.viewport.width, this.viewport.height);
                this.renderer.setPixelRatio = window.devicePixelRatio;
                this.container.appendChild(this.renderer.domElement);

                // scene
                this.scene = new THREE.Scene();

                // camera
                this.camera = new THREE.PerspectiveCamera(
                    40,
                    this.viewport.aspectRatio,
                    0.1,
                    100
                );
                this.camera.position.set(0, 0, 3);

                // animation loop
                this.renderer.setAnimationLoop(this.render.bind(this))
            },
            get viewport() {
                let width = this.container.clientWidth
                let height = this.container.clientHeight
                let aspectRatio = width / height
                return {
                    width,
                    height,
                    aspectRatio
                }
            },
            onWindowResize() {
                this.camera.aspect = this.viewport.aspectRatio;
                this.camera.updateProjectionMatrix();
                this.renderer.setSize(this.viewport.width, this.viewport.height);
            },
            render() {
                // called every frame
                this.renderer.render(this.scene, this.camera);
            }
        },
        mounted() {
            this.setup();
        }
    }
</script>

<style scoped>

</style>