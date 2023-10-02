<template>
  <div class="menu">
    <div class="list">
      <canvas class="action_symbol"></canvas>
    </div>
    <canvas ref="canvas_symbols" class="canvas_symbols"></canvas>
  </div>
</template>

<script>
export default {
  methods: {
    func() {
      let canvas = this.$refs.canvas_symbols;
      var ctx = canvas.getContext("2d");
      ctx.canvas.width = 900;
      ctx.canvas.height = 570;

      var data =
        '<svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg"> \
    <defs> \
        <pattern id="smallGrid" width="10" height="10" patternUnits="userSpaceOnUse"> \
            <path d="M 10 0 L 0 0 0 10" fill="none" stroke="gray" stroke-width="0.5" /> \
        </pattern> \
        <pattern id="grid" width="80" height="80" patternUnits="userSpaceOnUse"> \
            <rect width="80" height="80" fill="url(#smallGrid)" /> \
            <path d="M 80 0 L 0 0 0 80" fill="none" stroke="gray" stroke-width="1" /> \
        </pattern> \
    </defs> \
    <rect width="100%" height="100%" fill="url(#grid)" /> \
</svg>';

      var DOMURL = window.URL || window.webkitURL || window;

      var img = new Image();
      var svg = new Blob([data], { type: "image/svg+xml;charset=utf-8" });
      var url = DOMURL.createObjectURL(svg);

      img.onload = function () {
        ctx.drawImage(img, 0, 0);
        DOMURL.revokeObjectURL(url);
      };
      img.src = url;
    },
  },
  mounted() {
    this.func();
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
  height: 100%;
  width: calc(100% - 140px);
  display: block;
  background-color: white;
}
</style>
