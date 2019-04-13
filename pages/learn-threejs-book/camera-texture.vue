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
      started: false,
      trackballControls: undefined,
      clock: undefined,
      videoTexture: undefined
    };
  },
  mounted() {
    this.setupCameraSceneRenderer();
    this.addLight();
    this.positionCamera();
    this.setupClock();
    this.setupTrackballControls();
    console.log(this.trackballControls);
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
        1000
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
      // create cube geometry and material
      for (let idx = 0; idx < 2; idx++) {
        const sphereGeometry = new THREE.SphereGeometry(10, 20, 20);
        const sphereMaterial = new THREE.MeshLambertMaterial({
          wireframe: false,
          map: this.videoTexture
        });
        const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
        sphere.position.set(
          Math.random() * 1,
          Math.random() * 2,
          Math.random() * 3
        );
        this.scene.add(sphere);
      }
    },
    /* start and render functions */
    startCamera() {
      const constraints = (window.constraints = {
        audio: false,
        video: { facingMode: "user" }
      });

      navigator.mediaDevices.getUserMedia(constraints).then(stream => {
        const videoTracks = stream.getVideoTracks();
        this.$refs.video.srcObject = stream;
        this.createVideoTexture();
        this.createBoxGeometry();
      });
    },
    startScene() {
      if (this.started) return;

      //this.startCamera();
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