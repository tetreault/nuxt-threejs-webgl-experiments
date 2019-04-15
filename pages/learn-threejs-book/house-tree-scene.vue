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
        70,
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
      this.camera.position.set(-20, 40, 30);
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
    createBoundingWall() {
      const wallLeft = new THREE.BoxGeometry(70, 2, 2);
      const wallRight = new THREE.BoxGeometry(70, 2, 2);
      const wallTop = new THREE.BoxGeometry(2, 2, 50);
      const wallBottom = new THREE.BoxGeometry(2, 2, 50);

      const wallMaterial = new THREE.MeshPhongMaterial({
        color: 0xa0522d
      });

      const wallLeftMesh = new THREE.Mesh(wallLeft, wallMaterial);
      const wallRightMesh = new THREE.Mesh(wallRight, wallMaterial);
      const wallTopMesh = new THREE.Mesh(wallTop, wallMaterial);
      const wallBottomMesh = new THREE.Mesh(wallBottom, wallMaterial);

      wallLeftMesh.position.set(15, 1, -25);
      wallRightMesh.position.set(15, 1, 25);
      wallTopMesh.position.set(-19, 1, 0);
      wallBottomMesh.position.set(49, 1, 0);

      this.scene.add(wallLeftMesh);
      this.scene.add(wallRightMesh);
      this.scene.add(wallTopMesh);
      this.scene.add(wallBottomMesh);
    },
    createGroundPlane() {
      const planeGeometry = new THREE.PlaneGeometry(70, 50);
      const planeMaterial = new THREE.MeshPhongMaterial({
        color: 0x9acd32
      });
      const plane = new THREE.Mesh(planeGeometry, planeMaterial);

      plane.receiveShadow = true;

      plane.rotation.x = -0.5 * Math.PI;
      plane.position.x = 15;
      plane.position.y = 0;
      plane.position.z = 0;

      this.scene.add(plane);
    },
    createHouse() {
      const roof = new THREE.ConeGeometry(5, 4);
      const base = new THREE.CylinderGeometry(5, 5, 6);
      const roofMaterial = new THREE.MeshPhongMaterial({
        color: 0x8b7213
      });
      const baseMaterial = new THREE.MeshPhongMaterial({
        color: 0xffe4c4
      });
      const roofMesh = new THREE.Mesh(roof, roofMaterial);
      const baseMesh = new THREE.Mesh(base, baseMaterial);

      roofMesh.position.set(25, 8, 0);
      baseMesh.position.set(25, 3, 0);

      roofMesh.receiveShadow = true;
      baseMesh.receiveShadow = true;
      roofMesh.castShadow = true;
      baseMesh.castShadow = true;

      this.scene.add(roofMesh);
      this.scene.add(baseMesh);
    },
    createTree() {
      const trunk = new THREE.BoxGeometry(1, 8, 1);
      const leaves = new THREE.SphereGeometry(4);
      const trunkMaterial = new THREE.MeshPhongMaterial({
        color: 0x8b4513
      });
      const leavesMaterial = new THREE.MeshPhongMaterial({
        color: 0x00ff00
      });
      const trunkMesh = new THREE.Mesh(trunk, trunkMaterial);
      const leavesMesh = new THREE.Mesh(leaves, leavesMaterial);

      trunkMesh.position.set(-10, 4, 0);
      leavesMesh.position.set(-10, 12, 0);

      trunkMesh.castShadow = true;
      trunkMesh.receiveShadow = true;
      leavesMesh.castShadow = true;
      leavesMesh.receiveShadow = true;

      this.scene.add(trunkMesh);
      this.scene.add(leavesMesh);
    },
    /* start and render functions */
    startScene() {
      if (this.started) return;

      this.createBoundingWall();
      this.createGroundPlane();
      this.createHouse();
      this.createTree();
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