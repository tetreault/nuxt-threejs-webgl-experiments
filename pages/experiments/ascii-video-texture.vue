<template>
  <section class="section">
    <div class="hero is-fullheight">
      <canvas 
        id="video-canvas" 
        ref="videoCanvas" 
        height="125" 
        width="200"/>
      <video
        id="video"
        ref="video"
        autoplay
        loop
        playsinline
        muted
        src="/videos/nic-cage-smile.mp4"
      />
      <div 
        id="three-element" 
        ref="threeElement"/>
      <button @click="startScene">Start</button>
    </div>
  </section>
</template>

<script>
import * as THREE from "three-full";

//
// utilizing this example: https://github.com/mrdoob/three.js/blob/master/examples/webgl_materials_video.html
//

export default {
  components: {},
  data() {
    return {
      scene: undefined,
      camera: undefined,
      renderer: undefined,
      effect: undefined,
      started: false,
      trackballControls: undefined,
      clock: undefined,
      videoTexture: undefined,
      start: undefined
    };
  },
  mounted() {
    this.start = Date.now();
    this.setupCameraSceneRenderer();
    this.setupASCIIEffect();
    this.addLight();
    this.setupClock();
    this.setupTrackballControls();
    this.bindWindowEvents();
  },
  methods: {
    /* THREE scene setup functions */
    setupCameraSceneRenderer() {
      // set up scene, camera, renderer, and axes
      this.scene = new THREE.Scene();
      this.camera = new THREE.PerspectiveCamera(
        70,
        window.innerWidth / window.innerHeight,
        1,
        1000
      );
      this.camera.position.y = 150;
      this.camera.position.z = 150;

      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setClearColor(new THREE.Color(0x000000));
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },
    setupASCIIEffect() {
      this.effect = new THREE.AsciiEffect(this.renderer, ".:-+*%@#", {
        invert: true
      });
      this.effect.setSize(window.innerWidth, window.innerHeight);
      this.effect.domElement.style.color = "white";
      this.effect.domElement.style.backgroundColor = "black";
      this.$refs.threeElement.appendChild(this.effect.domElement);
    },
    addLight() {
      // add point light
      const light1 = new THREE.PointLight(0xffffff);
      light1.position.set(500, 500, 500);
      this.scene.add(light1);

      const light2 = new THREE.PointLight(0xffffff);
      light2.position.set(-500, -500, -500);
      this.scene.add(light2);
    },
    setupClock() {
      this.clock = new THREE.Clock();
    },
    setupTrackballControls() {
      this.trackballControls = new THREE.TrackballControls(
        this.camera,
        document
      );
      this.trackballControls.rotateSpeed = 1.7;
    },
    /* textures, geometries, meshes */
    createVideoTexture() {
      this.videoTexture = new THREE.VideoTexture(this.$refs.video);
      this.videoTexture.minFilter = THREE.LinearFilter;
      this.videoTexture.magFilter = THREE.LinearFilter;
      this.videoTexture.format = THREE.RGBFormat;
    },
    createBoxGeometry() {
      const cubeGeometry = new THREE.BoxGeometry(200, 200, 200);
      const cubeMaterial = new THREE.MeshLambertMaterial({
        flatShading: true,
        map: this.videoTexture
      });
      const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
      this.scene.add(cube);
    },
    /* start and render functions */
    startScene() {
      if (this.started) return;

      this.createVideoTexture();
      this.createBoxGeometry();
      this.renderScene();

      this.started = !this.started;
    },
    renderCameraToCanvas() {
      const videoCanvasContext = this.$refs.videoCanvas.getContext("2d");

      videoCanvasContext.drawImage(
        this.$refs.video,
        0,
        0,
        this.$refs.videoCanvas.width,
        this.$refs.videoCanvas.height
      );
    },
    renderScene() {
      this.renderCameraToCanvas();

      const timer = Date.now() - this.start;
      const sphere = this.scene.children[2];
      //sphere.position.y = Math.abs(Math.sin(timer * 0.002)) * 150;
      // sphere.rotation.x = timer * 0.0003;
      // sphere.rotation.z = timer * 0.0002;

      this.trackballControls.update(this.clock.getDelta());
      this.effect.render(this.scene, this.camera);
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
#video-canvas,
#video {
  position: absolute;
  top: 10px;
  left: 10px;
  height: 125px;
  width: 200px;
  background: white;
}

#video {
  opacity: 0;
}

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