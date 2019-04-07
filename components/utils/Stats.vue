<template>
  <div ref="statsContainer" class="stats-container"/>
</template>

<script>
export default {
  data() {
    return {
      mode: 0,
      frames: 0,
      beginTime: undefined,
      prevTime: undefined
    };
  },
  mounted() {
    this.beginTime = (performance || Date).now();
    this.prevTime = beginTime;
  },
  methods: {
    setupPanel(name, fgColor, bgColor) {
      const min = Infinity;
      const max = 0;
      const round = Math.round;
      const PR = round(window.devicePixelRatio || 1);
      const WIDTH = 80 * PR;
      const HEIGHT = 48 * PR;
      const TEXT_X = 3 * PR;
      const TEXT_Y = 2 * PR;
      const GRAPH_X = 3 * PR;
      const GRAPH_Y = 15 * PR;
      const GRAPH_WIDTH = 74 * PR;
      const GRAPH_HEIGHT = 30 * PR;
      const canvas = document.createElement("canvas");
      const context = canvas.getContext("2d");

      canvas.width = WIDTH;
      canvas.height = HEIGHT;
      canvas.style.width = "80px;";
      canvas.style.height = "48px";

      context.font = "bold " + 9 * PR + "px Helvetica,Arial,sans-serif";
      context.textBaseline = "top";
      context.fillStyle = bgColor;
      context.fillRect(0, 0, WIDTH, HEIGHT);
      context.fillStyle = fgColor;
      context.fillText(name, TEXT_X, TEXT_Y);
      context.fillRect(GRAPH_X, GRAPH_Y, GRAPH_WIDTH, GRAPH_HEIGHT);
      context.fillStyle = bgColor;
      context.globalAlpha = 0.9;
      context.fillRect(GRAPH_X, GRAPH_Y, GRAPH_WIDTH, GRAPH_HEIGHT);
    }
  }
};
</script>

<style scoped>
.stats-container {
  position: fixed;
  top: 0;
  left: 0;
  cursor: pointer;
  opacity: 0.9;
  z-index: 10000;
}
</style>