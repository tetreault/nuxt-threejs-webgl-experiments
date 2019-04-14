<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div 
        id="three-element" 
        ref="threeElement"/>
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
        45,
        window.innerWidth / window.innerHeight,
        0.1,
        100
      );
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
      spotLight.position.set(-40, 60, -10);
      spotLight.shadow.mapSize = new THREE.Vector2(1024, 1024);
      spotLight.shadow.camera.far = 100;
      spotLight.shadow.camera.near = 10;
      spotLight.castShadow = true;
      spotLight.decay = 2;
      spotLight.penumbra = 0.05;
      this.scene.add(spotLight);

      const ambientLight = new THREE.AmbientLight(0x3c3c3c);
      this.scene.add(ambientLight);
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
      this.trackballControls = new THREE.TrackballControls(this.camera);
      this.trackballControls.rotateSpeed = 1.7;
    },
    /* textures, geometries, meshes */
    createParticles() {
      this.particles = new THREE.Geometry();

      for (let idx = 0; idx < this.particleCount; idx++) {
        const x = Math.random() * 10;
        const y = Math.random() * 10;
        const z = Math.random() * 4;
        const particle = new THREE.Vector3(x, y, z);

        particle.velocity = new THREE.Vector3(0, -Math.random(), 0);

        this.particles.vertices.push(particle);
      }

      this.createParticleSystem();
    },
    createParticleSystem() {
      const path = "/images/jon-snow.png";
      const loader = new THREE.TextureLoader();

      // need to load the PNG as a texture we can map to PointsMaterial
      loader.load(path, texture => {
        // manipulate texture values because my PNG is loading upside down
        texture.center = new THREE.Vector2(0.5, 0.5);
        texture.rotation = -2;
        texture.wrapS = THREE.RepeatWrapping;

        const particleMaterial = new THREE.PointsMaterial({
          color: 0xffffff,
          size: 3,
          map: texture,
          blending: THREE.AdditiveBlending,
          transparent: true
        });

        // create the particle system (the contstructor has been renamed from ParticleSystem to Points)
        this.particleSystem = new THREE.Points(
          this.particles,
          particleMaterial
        );
        this.particleSystem.sortParticles = true;

        // add the particle system to the screen
        this.scene.add(this.particleSystem);
      });
    },
    rotateParticleSystem(step) {
      if (typeof this.particleSystem === "undefined") return;

      this.particleSystem.rotation.y += step;
    },
    /* start and render functions */
    startScene() {
      if (this.started) return;

      this.createParticles();
      this.renderScene();

      this.started = !this.started;
    },
    renderScene() {
      this.trackballControls.update(this.clock.getDelta());
      this.rotateParticleSystem(0.005);
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