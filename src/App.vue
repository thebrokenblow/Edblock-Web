<template>
  <div class="menu">
    <div class="symbols">
      <canvas class="action_symbol"></canvas>
    </div>
    <canvas
      v-on:mousedown="mouseDown"
      v-on:mouseup="mouseUp"
      v-on:mouseout="mouseOut"
      v-on:mousemove="mouseMove"
      ref="canvasSymbols"
      class="canvas_symbols"
    ></canvas>
  </div>
  <svg
    width="100%"
    height="100%"
    class="grid_canvas_symbols"
    xmlns="http://www.w3.org/2000/svg"
  >
    <defs>
      <pattern
        id="smallGrid"
        width="10"
        height="10"
        patternUnits="userSpaceOnUse"
      >
        <path
          d="M 10 0 L 0 0 0 10"
          fill="none"
          stroke="gray"
          stroke-width="0.5"
        />
      </pattern>
      <pattern id="grid" width="80" height="80" patternUnits="userSpaceOnUse">
        <rect width="80" height="80" fill="url(#smallGrid)" />
        <path
          d="M 80 0 L 0 0 0 80"
          fill="none"
          stroke="gray"
          stroke-width="1"
        />
      </pattern>
    </defs>

    <rect width="100%" height="100%" fill="url(#grid)" />
  </svg>
</template>

<script>
export default {
  data() {
    return {
      canvasSymbols: null,
      contextCanvasSymbols: null,
      startX: 0,
      startY: 0,
      shapes: [{ x: 0, y: 0, width: 140, height: 60, color: "blue" }],
      currentShapeIndex: 0,
      isDragging: false,
    };
  },

  mounted() {
    this.drawCanvas();
  },

  methods: {
    mouseUp(event) {
      if (!this.isDragging) {
        return;
      }

      event.preventDefault();
      this.isDragging = false;
    },

    mouseOut(event) {
      if (!this.isDragging) {
        return;
      }

      event.preventDefault();
      this.isDragging = false;
    },

    mouseDown(event) {
      event.preventDefault();
      this.startX = parseInt(event.clientX);
      this.startY = parseInt(event.clientY);
      let indexShape = 0;
      for (const shape of this.shapes) {
        if (this.isMouseInShape(this.startX, this.startY, shape)) {
          this.currentShapeIndex = indexShape;
          this.isDragging = true;
        }
        indexShape++;
      }
    },

    mouseMove(event) {
      if (!this.isDragging) {
        return;
      }

      event.preventDefault();

      let mouseX = parseInt(event.clientX);
      let mouseY = parseInt(event.clientY);

      this.currentShape = this.shapes[this.currentShapeIndex];

      this.currentShape.x = mouseX - (mouseX % 10) - 140;
      this.currentShape.y = mouseY - (mouseY % 10);

      this.drawSymbols();
    },

    drawSymbols() {
      const width = this.canvasSymbols.width;
      const height = this.canvasSymbols.height;

      this.contextCanvasSymbols.clearRect(0, 0, width, height);

      for (const shape of this.shapes) {
        this.contextCanvasSymbols.fillStyle = shape.color;
        this.contextCanvasSymbols.fillRect(
          shape.x,
          shape.y,
          shape.width,
          shape.height
        );
      }
    },

    drawCanvas() {
      this.canvasSymbols = this.$refs.canvasSymbols;
      this.contextCanvasSymbols = this.canvasSymbols.getContext("2d");

      this.canvasSymbols.width = window.innerWidth;
      this.canvasSymbols.height = window.innerHeight;
      this.drawSymbols();
    },

    isMouseInShape(x, y, shape) {
      const shapeLeft = shape.x;
      const shapeRight = shape.x + shape.width + 140;
      const shapeTop = shape.y;
      const shapeBottom = shape.y + shape.height;
      if (x > shapeLeft && x < shapeRight && y > shapeTop && y < shapeBottom) {
        return true;
      }

      return false;
    },
  },
};
</script>

<style>
@import "./reset.css";
html,
body {
  height: 100%;
  width: 100%;
}

.menu {
  display: flex;
  height: 100%;
}

.action_symbol {
  width: 140px;
  height: 60px;
  background-color: green;
}

.canvas_symbols {
  width: calc(100% - 140);
  position: absolute;
  margin: 0 0 0 140px;
}

.symbols {
  position: fixed;
}

.grid_canvas_symbols {
  height: 1000px;
  margin: 0 0 0 140px;
}
</style>
