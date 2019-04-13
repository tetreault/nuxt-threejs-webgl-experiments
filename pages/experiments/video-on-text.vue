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
import { Vector3 } from "three";

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
      videoTexture: undefined,
      text: "WTF",
      textSettings: {
        height: 2,
        size: 1,
        hover: 3,
        curveSegments: 4,
        bevelThickness: 0.02,
        bevelSize: 0.05,
        bevelEnabled: true,
        font: undefined,
        fontName: "helvetiker",
        fontWeight: "bold",
        mirror: false
      },
      group: undefined
    };
  },
  mounted() {
    this.setupCameraSceneRenderer();
    this.addLight();
    this.positionCamera();
    this.setupClock();
    this.setupTrackballControls();
    this.setupGroup();
    this.bindWindowEvents();
    this.loadFont();
    this.$refs.threeElement.appendChild(this.renderer.domElement);
  },
  methods: {
    /* THREE scene setup functions */
    setupCameraSceneRenderer() {
      // set up scene
      this.scene = new THREE.Scene();
      this.scene.background = new THREE.Color(0x000000);
      this.scene.fog = new THREE.Fog(0x000000, 250, 1400);

      // set up camera
      this.camera = new THREE.PerspectiveCamera(
        70,
        window.innerWidth / window.innerHeight,
        0.1,
        50
      );
      this.camera.position.z = 8;

      // set up renderer
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setClearColor(new THREE.Color(0x000000));
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    },
    addLight() {
      // add spotlight
      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.125);
      directionalLight.position.set(0, 0, 1).normalize();
      this.scene.add(directionalLight);

      // add point light
      const pointLight = new THREE.PointLight(0xffffff, 1.5);
      pointLight.position.set(0, 100, 90);
      this.scene.add(pointLight);

      // add ambient light
      const ambientLight = new THREE.AmbientLight(0x3c3c3c);
      this.scene.add(ambientLight);
    },
    positionCamera() {
      // position the camera for the scene
      //this.camera.position.set(-30, 40, 30);
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
    setupGroup() {
      this.group = new THREE.Group();
      this.group.position.y = 100;
      this.scene.add(this.group);
    },
    /* textures, geometries, meshes, fonts */
    loadFont() {
      const fontPath = `/jsonFontFace/${this.textSettings.fontName}_${
        this.textSettings.fontWeight
      }.typeface.json`;
      const fontLoader = new THREE.FontLoader();

      fontLoader.load(fontPath, font => {
        this.textSettings.font = font;

        // create the video texture and text geometry after the font has loaded
        this.createVideoTexture();
        this.createTextGeometry();
      });
    },
    createVideoTexture() {
      this.videoTexture = new THREE.VideoTexture(this.$refs.video);
      this.videoTexture.minFilter = THREE.LinearFilter;
      this.videoTexture.magFilter = THREE.LinearFilter;
      this.videoTexture.format = THREE.RGBFormat;
    },
    createTextGeometry() {
      const textMaterials = [
        new THREE.MeshLambertMaterial({
          flatShading: true,
          map: this.videoTexture
        }), // front
        new THREE.MeshLambertMaterial({
          map: this.videoTexture
        }) // side
      ];
      const textGeometryParams = {
        font: this.textSettings.font,
        size: this.textSettings.size,
        height: this.textSettings.height,
        curveSegments: this.textSettings.curveSegments,
        bevelThickness: this.textSettings.bevelThickness,
        bevelSize: this.textSettings.bevelSize,
        bevelEnabled: this.textSettings.bevelEnabled
      };
      const textGeometry = new THREE.TextGeometry(
        this.text,
        textGeometryParams
      );
      const textMesh = new THREE.Mesh(textGeometry, textMaterials);
      this.scene.add(textMesh);
    },
    /* start and render functions */
    startScene() {
      if (this.started) return;

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