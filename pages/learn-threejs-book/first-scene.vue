<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div 
        id="three-element" 
        ref="threeElement"/>
    </div>
  </section>
</template>

<script>
import * as THREE from "three";

export default {
  data() {
    return {
      scene: undefined,
      camera: undefined,
      renderer: undefined
    };
  },
  mounted() {
    this.setup();
    this.addGeometry();
    this.positionCamera();
    this.attachAndRender();
  },
  methods: {
    setup() {
      // set up scene, camera, renderer, and axes
      const axes = new THREE.AxesHelper(20);

      this.scene = new THREE.Scene();
      this.camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setClearColor(new THREE.Color(0x000000));
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.scene.add(axes);

      window.addEventListener("resize", () => {
        this.renderer.setSize(window.innerWidth, window.innerHeight);
      });
    },
    addGeometry() {
      // create plane geometry and material
      const planeGeometry = new THREE.PlaneGeometry(60, 20);
      const planeMaterial = new THREE.MeshBasicMaterial({ color: 0xaaaaaa });
      const plane = new THREE.Mesh(planeGeometry, planeMaterial);

      // create cube geometry and material
      const cubeGeometry = new THREE.BoxGeometry(4, 4, 4);
      const cubeMaterial = new THREE.MeshBasicMaterial({
        color: 0xff0000,
        wireframe: false
      });
      const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
      cube.position.set(-4, 3, 0);

      // create sphere geometry and material
      const sphereGeometry = new THREE.SphereGeometry(4, 20, 20);
      const sphereMaterial = new THREE.MeshBasicMaterial({
        color: 0x7777ff,
        wireframe: false
      });
      const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
      sphere.position.set(20, 4, 2);

      // add plane to the scene
      plane.rotation.x = -0.5 * Math.PI;
      plane.position.set(15, 0, 0);
      this.scene.add(plane);

      // add cube to the scene
      this.scene.add(cube);

      // add sphere to the scene
      this.scene.add(sphere);
    },
    positionCamera() {
      // position the camera for the scene
      this.camera.position.set(-30, 40, 30);
      this.camera.lookAt(this.scene.position);
    },
    attachAndRender() {
      // attach the renderer's DOM element to the scene and then render
      this.$refs.threeElement.appendChild(this.renderer.domElement);
      this.renderer.render(this.scene, this.camera);
    }
  }
};
</script>
