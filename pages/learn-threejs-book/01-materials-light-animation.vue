<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div 
        id="three-element" 
        ref="threeElement"/>
      <button @click.prevent="startScene">Start</button>
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
      renderer: undefined,
      stats: undefined
    };
  },
  mounted() {
    this.setup();
    this.addGeometry();
    this.addLight();
    this.positionCamera();
    this.$refs.threeElement.appendChild(this.renderer.domElement);
  },
  methods: {
    setup() {
      // set up scene, camera, renderer, and axes
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
      this.renderer.shadowMap.enabled = true;

      window.addEventListener("resize", () => {
        this.renderer.setSize(window.innerWidth, window.innerHeight);
      });
    },
    addGeometry() {
      // create plane geometry and material
      const planeGeometry = new THREE.PlaneGeometry(60, 20);
      const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xaaaaaa });
      const plane = new THREE.Mesh(planeGeometry, planeMaterial);
      plane.rotation.x = -0.5 * Math.PI;
      plane.position.set(15, 0, 0);
      plane.receiveShadow = true;

      // create cube geometry and material
      const cubeGeometry = new THREE.BoxGeometry(4, 4, 4);
      const cubeMaterial = new THREE.MeshLambertMaterial({
        color: 0xff0000,
        wireframe: false
      });
      const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
      cube.position.set(-4, 3, 0);
      cube.castShadow = true;

      // create sphere geometry and material
      const sphereGeometry = new THREE.SphereGeometry(4, 20, 20);
      const sphereMaterial = new THREE.MeshLambertMaterial({
        color: 0x7777ff,
        wireframe: false
      });
      const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
      sphere.position.set(20, 4, 2);
      sphere.castShadow = true;

      // add geometries to the scene
      this.scene.add(plane);
      this.scene.add(cube);
      this.scene.add(sphere);
    },
    addLight() {
      const spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 40, -15);
      spotLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
      spotLight.shadow.camera.far = 130;
      spotLight.shadow.camera.near = 40;
      spotLight.castShadow = true;
      this.scene.add(spotLight);
    },
    positionCamera() {
      // position the camera for the scene
      this.camera.position.set(-30, 40, 30);
      this.camera.lookAt(this.scene.position);
    },
    renderScene() {
      window.RAF = requestAnimationFrame(this.renderScene);
      this.renderer.render(this.scene, this.camera);
    },
    startScene() {
      this.renderScene();
    }
  }
};
</script>

<style scoped>
button {
  position: absolute;
  top: 10px;
  left: 0;
  right: 0;
  margin: auto;
  display: block;
  width: 80px;
  background: red;
  color: white;
  border-color: transparent;
  border-radius: 10px;
  outline: unset;
}
</style>