<template>
  <div class="menu">
    <div class="symbols">
      <canvas @click="addSymol" class="action_symbol"></canvas>
    </div>
    <canvas
      v-on:mousedown="mouseDown"
      v-on:mouseup="mouseUp"
      v-on:mouseout="mouseOut"
      v-on:mousemove="mouseMove"
      ref="canvasSymbols"
      class="canvas_symbols"
    >
    </canvas>
    <svg
      class="grid_canvas_symbols"
      width="100%"
      height="100%"
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
  </div>
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

      [this.startX, this.startY] = this.roundingCoordinates(
        this.startX,
        this.startY
      );

      let indexShape = 0;
      for (const shape of this.shapes) {
        if (this.isMouseInShape(this.startX, this.startY, shape)) {
          this.currentShapeIndex = indexShape;
          this.isDragging = true;
        }
        indexShape++;
      }
    },

    roundingCoordinates(x, y) {
      x -= x % 10;
      y -= y % 10;
      return [x, y];
    },

    mouseMove(event) {
      if (!this.isDragging) {
        return;
      }

      event.preventDefault();

      let mouseX = parseInt(event.clientX);
      let mouseY = parseInt(event.clientY);
      [mouseX, mouseY] = this.roundingCoordinates(mouseX, mouseY);

      this.currentShape = this.shapes[this.currentShapeIndex];

      let dx = mouseX - this.startX;
      let dy = mouseY - this.startY;

      this.currentShape.x += dx;
      this.currentShape.y += dy;

      this.drawSymbols();

      this.startX = mouseX;
      this.startY = mouseY;
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

    addSymol() {
      this.shapes.push({ x: 0, y: 0, width: 140, height: 60, color: "blue" });
      this.drawSymbols();
    },
    drawCanvas() {
      this.canvasSymbols = this.$refs.canvasSymbols;
      this.contextCanvasSymbols = this.canvasSymbols.getContext("2d");

      this.canvasSymbols.width = window.innerWidth - 140;
      this.canvasSymbols.height = window.innerHeight;
      this.drawSymbols();
    },

    isMouseInShape(x, y, shape) {
      x -= 140;
      const shapeLeft = shape.x;
      const shapeRight = shape.x + shape.width;
      const shapeTop = shape.y;
      const shapeBottom = shape.y + shape.height;

      console.log(y, shapeTop);
      if (
        x >= shapeLeft &&
        x <= shapeRight &&
        y >= shapeTop &&
        y <= shapeBottom
      ) {
        return true;
      }

      return false;
    },
  },
};
</script>

<style>
@import "./reset.css";

.menu {
  display: flex;
}

.action_symbol {
  width: 140px;
  height: 60px;
  background-color: gold;
}

.grid_canvas_symbols {
  margin: 0 0 0 140px;
  height: 840px;
}

.canvas_symbols {
  position: absolute;
  margin: 0 0 0 140px;
}

.symbols {
  position: fixed;
  width: 140px;
}
</style>
