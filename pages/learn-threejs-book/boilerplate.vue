<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">WebGL - Boilerplate</h1>
      </div>
      <canvas 
        id="canvas" 
        ref="canvas">
        Your browser does not seem to support
        HTML5 canvas.
      </canvas>
    </div>
  </section>
</template>

<script>
import * as THREE from "three";

export default {
  data() {
    return {};
  },
  mounted() {
    this.$refs.canvas.width = this.$refs.canvas.clientWidth;
    this.$refs.canvas.height = this.$refs.canvas.clientHeight;

    console.log(THREE.REVISION);
  },
  methods: {}
};
</script>

<style>
h1.title {
  color: white;
}

#canvas {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background-color: black;
  z-index: -1;
}
</style>
