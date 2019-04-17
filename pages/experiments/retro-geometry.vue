<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div 
        id="three-element" 
        ref="threeElement" 
        tabindex="0"/>
      <button @click="startScene">Start</button>
    </div>
  </section>
</template>

<script>
import * as THREE from "three-full";

export default {
  components: {},
  data() {
    return {
      scene: undefined,
      camera: undefined,
      renderer: undefined,
      particleCount: 500,
      particles: undefined,
      particleSystem: undefined,
      started: false,
      trackballControls: undefined,
      clock: undefined
    };
  },
  mounted() {
    this.setupCameraSceneRenderer();
    this.addLight();
    this.positionCamera();
    this.setupClock();
    this.setupTrackballControls();
    this.bindWindowEvents();
    this.$refs.threeElement.appendChild(this.renderer.domElement);
  },
  methods: {
    /* THREE scene setup functions */
    setupCameraSceneRenderer() {
      // set up scene, camera, renderer, and axes
      this.scene = new THREE.Scene();

      this.camera = new THREE.PerspectiveCamera(
        40,
        window.innerWidth / window.innerHeight,
        0.1,
        10000
      );

      const axesHelper = new THREE.AxesHelper(500);
      this.scene.add(axesHelper);

      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setClearColor(new THREE.Color(0x000000));
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer.shadowMap.enabled = true;
      this.renderer.shadowMapSoft = true;
      this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    },
    addLight() {
      // add spotlight and ambient light
      const spotLight = new THREE.DirectionalLight(0xffffff);
      spotLight.position.set(-30, 40, -10);
      spotLight.shadow.mapSize = new THREE.Vector2(2048, 2048);
      spotLight.castShadow = true;
      this.scene.add(spotLight);

      const ambientLight = new THREE.AmbientLight(0x3c3c3c);
      this.scene.add(ambientLight);
    },
    positionCamera() {
      // position the camera for the scene
      this.camera.position.set(-9, 3.5, -12);
      this.camera.lookAt(this.scene.position);
    },
    setupClock() {
      this.clock = new THREE.Clock();
    },
    setupTrackballControls() {
      this.trackballControls = new THREE.TrackballControls(
        this.camera,
        this.$refs.threeElement.$el
      );
      console.log(this.trackballControls);
      this.trackballControls.domElement = this.$refs.threeElement.$el;
      this.trackballControls.rotateSpeed = 1.7;
    },
    /* textures, geometries, meshes */
    createPoints() {
      const geometry = new THREE.BufferGeometry();
      const vertices = [];
      for (var i = 0; i < 10000; i++) {
        vertices.push(THREE._Math.randFloatSpread(20)); // x
        vertices.push(THREE._Math.randFloatSpread(20)); // y
        vertices.push(THREE._Math.randFloatSpread(20)); // z
      }

      geometry.addAttribute(
        "position",
        new THREE.Float32BufferAttribute(vertices, 3)
      );
      const particles = new THREE.Points(
        geometry,
        new THREE.PointsMaterial({ color: 0x888888 })
      );
      this.scene.add(particles);
    },
    createGroundPlane() {
      let floorGeometry = new THREE.PlaneBufferGeometry(2000, 2000, 100, 100);
      floorGeometry.rotateX(-Math.PI / 2);
      console.log(floorGeometry);

      let position = floorGeometry.attributes.position;
      let vertex = new THREE.Vector3();

      for (let i = 0, l = position.count; i < l; i++) {
        vertex.fromBufferAttribute(position, i);
        vertex.x += Math.random() * 10;
        vertex.y += 1;
        vertex.z += 10;
        position.setXYZ(i, vertex.x, vertex.y, vertex.z);
      }

      floorGeometry = floorGeometry.toNonIndexed(); // ensure each face has unique vertices
      position = floorGeometry.attributes.position;

      const colors = [];
      let color = new THREE.Color();

      for (let i = 0, l = position.count; i < l; i++) {
        color.setHSL(Math.random(), Math.random(), Math.random());
        colors.push(color.r, color.g, color.b);
      }

      floorGeometry.addAttribute(
        "color",
        new THREE.Float32BufferAttribute(colors, 3)
      );

      const floorMaterial = new THREE.MeshBasicMaterial({
        vertexColors: THREE.VertexColors,
        opacity: 0.3
      });

      const floor = new THREE.Mesh(floorGeometry, floorMaterial);
      this.scene.add(floor);
    },
    /* start and render functions */
    startScene() {
      if (this.started) return;

      this.createGroundPlane();
      this.createPoints();
      this.renderScene();

      this.started = !this.started;
    },
    renderScene() {
      this.trackballControls.update(this.clock.getDelta());
      this.renderer.render(this.scene, this.camera);
      window.RAF = requestAnimationFrame(this.renderScene);
    },
    /* events */
    bindWindowEvents() {
      // window resize
      window.addEventListener("resize", this.handleWindowResize);
      // rotation keys
      window.addEventListener("keypress", this.changeCameraPosition);
    },
    handleWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },
    changeCameraPosition(e) {
      const theta = 0.2; //the speed of rotation
      const x = this.camera.position.x;
      const y = this.camera.position.y;
      const z = this.camera.position.z;

      e.preventDefault();

      switch (e.key) {
        case "a": // left
          this.camera.position.x = x * Math.cos(theta) + z * Math.sin(theta);
          this.camera.position.z = z * Math.cos(theta) - x * Math.sin(theta);
          break;
        case "w": // up
          this.camera.position.y = y * Math.cos(theta) - z * Math.sin(theta);
          this.camera.position.z = z * Math.cos(theta) + y * Math.sin(theta);
          break;
        case "s": // down
          this.camera.position.y = y * Math.cos(theta) + z * Math.sin(theta);
          this.camera.position.z = z * Math.cos(theta) - y * Math.sin(theta);
          break;
        case "d": // right
          this.camera.position.x = x * Math.cos(theta) - z * Math.sin(theta);
          this.camera.position.z = z * Math.cos(theta) + x * Math.sin(theta);
          break;
        default:
          break;
      }

      this.camera.lookAt(this.scene.position);
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