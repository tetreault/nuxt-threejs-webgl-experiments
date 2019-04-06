<template>
  <section class="section">
    <div class="hero is-fullheight">
      <div class="hero-head">
        <h1 class="title has-text-centered">WebGL - Basic GLSL shader</h1>
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
      vertexShaderCode: `
        #version 100
        void main() {
          gl_Position = vec4(0.0, 0.0, 0.0, 1.0);
          gl_PointSize = 164.0;
        }
      `,
      vertexShader: undefined,
      fragmentShaderCode: `
        #version 100
        void main() {
          gl_FragColor = vec4(0.88, 0.54, 0.34, 1.0);
        }
      `,
      fragmentShader: undefined,
      program: undefined,
      buffer: undefined
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

      // set up shaders and program
      this.setupVertexShader();
      this.setupFragmentShader();
      this.setupProgram();
    }
  },
  methods: {
    setupVertexShader() {
      // vertex shader handles geometry
      this.vertexShader = this.gl.createShader(this.gl.VERTEX_SHADER);
      this.gl.shaderSource(this.vertexShader, this.vertexShaderCode);
      this.gl.compileShader(this.vertexShader);
    },
    setupFragmentShader() {
      // fragment shader handles color
      this.fragmentShader = this.gl.createShader(this.gl.FRAGMENT_SHADER);
      this.gl.shaderSource(this.fragmentShader, this.fragmentShaderCode);
      this.gl.compileShader(this.fragmentShader);
    },
    setupProgram() {
      this.program = this.gl.createProgram();
      this.gl.attachShader(this.program, this.vertexShader);
      this.gl.attachShader(this.program, this.fragmentShader);
      this.gl.linkProgram(this.program);
      this.gl.detachShader(this.program, this.vertexShader);
      this.gl.detachShader(this.program, this.fragmentShader);
      this.gl.deleteShader(this.vertexShader);
      this.gl.deleteShader(this.fragmentShader);

      if (!this.gl.getProgramParameter(this.program, this.gl.LINK_STATUS)) {
        const linkErrLog = this.gl.getProgramInfoLog(this.program);

        this.cleanup();

        console.error(
          "Shader program did not link successfully. " +
            "Error log: " +
            linkErrLog
        );
        return;
      }

      this.initializeAttributes();

      this.gl.useProgram(this.program);
      this.gl.drawArrays(this.gl.POINTS, 0, 1);

      this.cleanup();
    },
    initializeAttributes() {
      this.gl.enableVertexAttribArray(0);
      this.buffer = this.gl.createBuffer();
      this.gl.bindBuffer(this.gl.ARRAY_BUFFER, this.buffer);
      this.gl.vertexAttribPointer(0, 1, this.gl.FLOAT, false, 0, 0);
    },
    cleanup() {
      this.gl.useProgram(null);
      if (this.buffer) this.gl.deleteBuffer(this.buffer);
      if (this.program) this.gl.deleteProgram(this.program);
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
