<template>
  <div class="container">
    <canvas
      ref="canvas"
      :width="800"
      :height="600"
    ></canvas>
    <div style="display: flex; gap: 10px;">
      <button @click="revealHitboxes">show hitboxes</button>
      <button @click="redraw">hide hitboxes</button>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { onMounted, ref } from "vue";
  import { circle, line } from "../Shapes.ts";

  const canvas = ref<HTMLCanvasElement | null>(null);
  const shapes = [
    circle({
      at: { x: 200, y: 200 },
      radius: 50,
      color: "red",
      stroke: { color: "black", width: 3 },
      text: "Circle",
    }),
    line({
      start: { x: 100, y: 100 },
      end: { x: 300, y: 300 },
      width: 10,
      text: "Line",
      color: "blue",
    }),
  ];

  const revealHitboxes = () => {
    if (!canvas.value) return;
    const ctx = canvas.value.getContext("2d");
    if (!ctx) return;

    for (let x = 0; x < canvas.value.width; x++) {
      for (let y = 0; y < canvas.value.height; y++) {
        // console.log(x, y)
        const point = { x, y };
        // PREDICATE: Array<Shape> prototype -> .some (arg: Shape) => boolean
        const isThisPointInsideAShape = shapes.some((shape) =>
          shape.hitbox(point)
        );
        const IN_HITBOX_COLOR = "rgba(255, 0, 0, .25)";
        const NOT_IN_HITBOX_COLOR = "rgba(0, 255, 0, .25)";
        const color = isThisPointInsideAShape
          ? IN_HITBOX_COLOR
          : NOT_IN_HITBOX_COLOR;
        const dot = circle({
          radius: 1,
          at: { x, y },
          color,
          text: "",
        });
        dot.draw(ctx);
      }
    }
  };

  const redraw = () => {
    if (!canvas.value) return;
    const ctx = canvas.value.getContext("2d");
    if (!ctx) return;
    ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
    shapes.forEach((shape) => shape.draw(ctx));
  };

  onMounted(() => {
    redraw();
  });
</script>

<style scoped>
  canvas {
    border: 1px solid black;
  }

  div.container {
    display: flex;
    align-items: center;
    width: 100vw;
    flex-direction: column;
    gap: 10px;
  }
</style>
