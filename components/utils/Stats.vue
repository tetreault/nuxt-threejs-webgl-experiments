<template>
  <div 
    ref="statsContainer" 
    class="stats-container"/>
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
    this.prevTime = this.beginTime;
    this.setupPanel("fps", "#0ff", "#002");
    this.setupPanel("ms", "#0f0", "#020");
  },
  methods: {
    setupPanel(name, fgColor, bgColor) {
      const min = Infinity;
      const max = 0;
      const round = Math.round;
      const PR = Math.round(window.devicePixelRatio || 1);
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

      canvas.dataset.min = min;
      canvas.dataset.max = max;
      canvas.dataset.round = round;
      canvas.dataset.PR = PR;
      canvas.dataset.fg = fgColor;
      canvas.dataset.bg = bgColor;
      canvas.dataset.WIDTH = WIDTH;
      canvas.dataset.HEIGHT = HEIGHT;
      canvas.dataset.TEXT_X = TEXT_X;
      canvas.dataset.TEXT_Y = TEXT_Y;
      canvas.dataset.GRAPH_X = GRAPH_X;
      canvas.dataset.GRAPH_Y = GRAPH_Y;
      canvas.dataset.GRAPH_WIDTH = GRAPH_WIDTH;
      canvas.dataset.GRAPH_HEIGHT = GRAPH_HEIGHT;

      canvas.id = name;
      canvas.width = WIDTH;
      canvas.height = HEIGHT;
      canvas.style.width = "80px;";
      canvas.style.height = "48px";

      this.$refs.statsContainer.appendChild(canvas);

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
    },
    update() {
      min = Math.min(min, value);
      max = Math.max(max, value);

      context.fillStyle = bg;
      context.globalAlpha = 1;
      context.fillRect(0, 0, WIDTH, GRAPH_Y);
      context.fillStyle = fg;
      context.fillText(
        round(value) + " " + name + " (" + round(min) + "-" + round(max) + ")",
        TEXT_X,
        TEXT_Y
      );

      context.drawImage(
        canvas,
        GRAPH_X + PR,
        GRAPH_Y,
        GRAPH_WIDTH - PR,
        GRAPH_HEIGHT,
        GRAPH_X,
        GRAPH_Y,
        GRAPH_WIDTH - PR,
        GRAPH_HEIGHT
      );

      context.fillRect(GRAPH_X + GRAPH_WIDTH - PR, GRAPH_Y, PR, GRAPH_HEIGHT);

      context.fillStyle = bg;
      context.globalAlpha = 0.9;
      context.fillRect(
        GRAPH_X + GRAPH_WIDTH - PR,
        GRAPH_Y,
        PR,
        round((1 - value / maxValue) * GRAPH_HEIGHT)
      );
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