<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">WebGL - Scissor Square Game</h1>
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
      gl: undefined,
      rectangle: {
        color: [0, 0, 0],
        size: [0, 0],
        position: [0, 0]
      }
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
      this.gl.viewport(
        0,
        0,
        this.gl.drawingBufferWidth,
        this.gl.drawingBufferHeight
      );

      // Set the WebGLRenderingContext clear color to the random color.
      this.gl.clearColor(0.0, 0.0, 0.0, 1.0);

      // Clear the context with the newly set color.
      this.gl.clear(this.gl.COLOR_BUFFER_BIT);

      // set up rectangle
      this.setupRectangle();

      setInterval(this.drawAnimation, 17);
    }
  },
  methods: {
    setupRectangle() {
      const randNums = this.getRandomVector();

      // We get three random numbers and use them for new rectangle
      // size and position. For each we use a different number,
      // because we want horizontal size, vertical size and
      // position to be determined independently.
      this.rectangle.size = [120 * randNums[0], 120 * randNums[1]];

      this.rectangle.position = [
        randNums[2] * (this.gl.drawingBufferWidth - this.rectangle.size[0]),
        this.gl.drawingBufferHeight
      ];

      this.rectangle.velocity = 1.0 + 6.0 * Math.random();

      this.rectangle.color = this.getRandomVector();

      this.gl.clearColor(
        this.rectangle.color[0],
        this.rectangle.color[1],
        this.rectangle.color[2],
        1.0
      );
    },
    drawAnimation() {
      this.gl.scissor(
        this.rectangle.position[0],
        this.rectangle.position[1],
        this.rectangle.size[0],
        this.rectangle.size[1]
      );
      this.gl.clear(this.gl.COLOR_BUFFER_BIT);
      this.rectangle.position[1] -= this.rectangle.velocity;
      if (this.rectangle.position[1] < 0) {
        //misses += 1;
        //missesDisplay.innerHTML = misses;
        this.setupRectangle();
      }
    },
    getRandomVector() {
      return [Math.random(), Math.random(), Math.random()];
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
</style>
