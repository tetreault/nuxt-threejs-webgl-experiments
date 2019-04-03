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
      gl: undefined,
      size: [60, 60],
      velocity: 3.0,
      position: []
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
      this.gl.enable(this.gl.SCISSOR_TEST);
      this.gl.clearColor(0, 0, 0, 1.0);
      // Unlike the browser window, vertical position in WebGL is
      // measured from bottom to top. In here we set the initial
      // position of the square to be at the top left corner of the
      // drawing buffer.
      this.position = [0, this.gl.drawingBufferHeight];

      setInterval(() => {
        this.scissorAnimation();
      }, 15);
    }
  },
  methods: {
    getRandomColor() {
      return [Math.random(), Math.random(), Math.random()];
    },
    scissorAnimation() {
      let color;
      this.gl.scissor(
        this.position[0],
        this.position[1],
        this.size[0],
        this.size[1]
      );
      this.gl.clear(this.gl.COLOR_BUFFER_BIT);
      // Every frame the vertical position of the square is
      // decreased, to create the illusion of movement.
      this.position[1] -= this.velocity;
      // When the sqaure hits the bottom of the drawing buffer,
      // we override it with new square of different color and
      // velocity.
      if (this.position[1] < 0) {
        // Horizontal position chosen randomly, and vertical
        // position at the top of the drawing buffer.
        this.position = [
          Math.random() * (this.gl.drawingBufferWidth - this.size[0]),
          this.gl.drawingBufferHeight
        ];
        // Random velocity between 1.0 and 7.0
        this.velocity = 1.0 + 6.0 * Math.random();
        this.color = this.getRandomColor();
        this.gl.clearColor(this.color[0], this.color[1], this.color[2], 1.0);
      }
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
