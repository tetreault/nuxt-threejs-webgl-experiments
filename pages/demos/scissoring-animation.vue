<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">WebGL - Scissoring animation</h1>
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
export default {
  data() {
    return {
      gl: undefined
    };
  },
  mounted() {
    // The following two lines set the size (in CSS pixels) of
    // the drawing buffer to be identical to the size of the
    // canvas HTML element, as determined by CSS.
    this.$refs.canvas.width = this.$refs.canvas.clientWidth;
    this.$refs.canvas.height = this.$refs.canvas.clientHeight;

    this.gl =
      this.$refs.canvas.getContext("webgl") ||
      this.$refs.canvas.getContext("experimental-webgl");

    if (!this.gl) {
      alert(
        "Failed to get WebGL context. Your browser or device may not support WebGL."
      );
    } else {
      setInterval(() => {
        this.scissorAnimation();
      }, 15);
    }
  },
  methods: {
    scissorAnimation() {}
  }
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
