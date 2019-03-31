<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">WebGL - Color masking</h1>
      </div>
      <canvas 
        id="canvas" 
        ref="canvas">
        Your browser does not seem to support
        HTML5 canvas.
      </canvas>
      <div class="hero-body">
        <div class="btn-container">
          <button 
            ref="redBtn" 
            data-color="red" 
            @click.prevent="setColorMask">Red</button>
          <button 
            ref="greenBtn" 
            data-color="green" 
            @click.prevent="setColorMask">Green</button>
          <button 
            ref="blueBtn" 
            data-color="blue" 
            @click.prevent="setColorMask">Blue</button>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      gl: undefined,
      colorMask: {
        red: true,
        green: true,
        blue: true
      }
    };
  },
  mounted() {
    this.gl =
      this.$refs.canvas.getContext("webgl") ||
      this.$refs.canvas.getContext("experimental-webgl");

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
      const rgbArray = [Math.random(), Math.random(), Math.random()];
      this.gl.viewport(
        0,
        0,
        this.gl.drawingBufferWidth,
        this.gl.drawingBufferHeight
      );
      // Set the WebGLRenderingContext clear color to the random color.
      this.gl.clearColor(rgbArray[0], rgbArray[1], rgbArray[2], 1.0);
      // Clear the context with the newly set color.
      this.gl.clear(this.gl.COLOR_BUFFER_BIT);
    },
    setColorMask(e) {
      switch (e.target.dataset.color) {
        case "red":
          this.colorMask.red = !this.colorMask.red;
          break;
        case "green":
          this.colorMask.green = !this.colorMask.green;
          break;
        case "blue":
          this.colorMask.blue = !this.colorMask.blue;
          break;
        default:
          break;
      }

      this.gl.colorMask(
        this.colorMask.red,
        this.colorMask.green,
        this.colorMask.blue,
        true
      );
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
