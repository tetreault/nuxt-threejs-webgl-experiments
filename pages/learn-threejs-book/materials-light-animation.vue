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
let TrackballControls;

// avoid "window/document not defined" error
if (process.client) {
  TrackballControls = require("three-trackballcontrols");
}

export default {
  components: {},
  data() {
    return {
      scene: undefined,
      camera: undefined,
      renderer: undefined,
      stats: undefined,
      plane: undefined,
      cube: undefined,
      sphere: undefined,
      started: false,
      step: 0,
      trackballControls: undefined,
      clock: undefined
    };
  },
  mounted() {
    this.setup();
    this.addGeometry();
    this.addLight();
    this.positionCamera();
    this.setupClock();
    this.setupTrackballControls();
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
      this.renderer.shadowMapSoft = true;
      this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;

      window.addEventListener("resize", this.handleWindowResize);
    },
    handleWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },
    addGeometry() {
      // create plane geometry and material
      const planeGeometry = new THREE.PlaneGeometry(60, 20);
      const planeMaterial = new THREE.MeshLambertMaterial({ color: 0xaaaaaa });
      this.plane = new THREE.Mesh(planeGeometry, planeMaterial);
      this.plane.rotation.x = -0.5 * Math.PI;
      this.plane.position.set(15, 0, 0);
      this.plane.receiveShadow = true;

      // create cube geometry and material
      const cubeGeometry = new THREE.BoxGeometry(4, 4, 4);
      const cubeMaterial = new THREE.MeshLambertMaterial({
        color: 0xff0000,
        wireframe: false
      });
      this.cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
      this.cube.position.set(-4, 3, 0);
      this.cube.castShadow = true;

      // create sphere geometry and material
      const sphereGeometry = new THREE.SphereGeometry(4, 20, 20);
      const sphereMaterial = new THREE.MeshLambertMaterial({
        color: 0x7777ff,
        wireframe: false
      });
      this.sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
      this.sphere.position.set(20, 4, 2);
      this.sphere.castShadow = true;

      // add geometries to the scene
      this.scene.add(this.plane);
      this.scene.add(this.cube);
      this.scene.add(this.sphere);
    },
    updateGeometryRotation(shape, coords) {
      shape.rotation.x += coords[0];
      shape.rotation.y += coords[1];
      shape.rotation.z += coords[2];
    },
    updateGeometryPosition(shape, coords) {
      shape.position.x += coords[0];
      shape.position.y += coords[1];
    },
    addLight() {
      const spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 40, -15);
      spotLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
      spotLight.shadow.camera.far = 130;
      spotLight.shadow.camera.near = 40;
      spotLight.castShadow = true;
      spotLight.decay = 2;
      spotLight.penumbra = 0.05;
      this.scene.add(spotLight);
    },
    positionCamera() {
      // position the camera for the scene
      this.camera.position.set(-30, 40, 30);
      this.camera.lookAt(this.scene.position);
    },
    setupClock() {
      this.clock = new THREE.Clock();
    },
    setupTrackballControls() {
      this.trackballControls = new TrackballControls(
        this.camera,
        this.renderer.domElement
      );
      this.trackballControls.rotateSpeed = 1.0;
      this.trackballControls.zoomSpeed = 1.2;
      this.trackballControls.panSpeed = 0.8;
      this.trackballControls.noZoom = false;
      this.trackballControls.noPan = false;
      this.trackballControls.staticMoving = false;
      this.trackballControls.dynamicDampingFactor = 0.3;
      this.trackballControls.keys = [65, 83, 68];
    },
    renderScene() {
      this.step += 0.0004;

      const rotationArray = [0.02, 0.02, 0.02];
      const positionArray = [
        0.02 * Math.cos(this.step),
        0.02 * Math.abs(Math.sin(this.step))
      ];

      this.trackballControls.update(this.clock.getDelta());
      this.updateGeometryRotation(this.cube, rotationArray);
      this.updateGeometryPosition(this.sphere, positionArray);

      window.RAF = requestAnimationFrame(this.renderScene);
      this.renderer.render(this.scene, this.camera);
    },
    startScene() {
      if (this.started) return;

      this.renderScene();

      this.started = !this.started;
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