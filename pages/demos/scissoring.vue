<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">WebGL - Basic scissoring</h1>
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
      canvas.getContext("experimental-webgl");

    if (!this.gl) {
      alert(
        "Failed to get WebGL context. Your browser or device may not support WebGL."
      );
    } else {
      setInterval(() => {
        this.drawRandomColors();
      }, 1000);
    }
  },
  methods: {
    drawRandomColors() {
      const scissorDimensions = [
        Math.random() * this.$refs.canvas.width,
        Math.random() * this.$refs.canvas.width,
        Math.random() * this.$refs.canvas.height,
        Math.random() * this.$refs.canvas.height
      ];
      const rgbArray = [Math.random(), Math.random(), Math.random()];
      this.gl.viewport(
        0,
        0,
        this.gl.drawingBufferWidth,
        this.gl.drawingBufferHeight
      );

      // Enable scissoring operation and define the position and
      // size of the scissoring area.
      this.gl.enable(this.gl.SCISSOR_TEST);
      this.gl.scissor(
        scissorDimensions[0],
        scissorDimensions[1],
        scissorDimensions[2],
        scissorDimensions[3]
      );

      // Set the WebGLRenderingContext clear color to the random color.
      this.gl.clearColor(rgbArray[0], rgbArray[1], rgbArray[2], 1.0);
      // Clear the context with the newly set color.
      this.gl.clear(this.gl.COLOR_BUFFER_BIT);
    }
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

.hero-body {
  justify-content: center;
}

button {
  width: 60px;
  height: 20px;
  border-radius: 5px;
  margin: 5px;
}
</style>
