<template>
  <canvas ref="canvas" :width="800" :height="600" @click="handleClick"></canvas>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { circle, line, Circle, Line, Shape } from '../Shapes.ts'; 

const canvas = ref<HTMLCanvasElement | null>(null);
const shapes: Shape[] = [
  circle({
    at: { x: 200, y: 200 },
    radius: 50,
    color: 'red',
    stroke: { color: 'black', width: 3 },
    text: 'Circle',
  }),
  line({
    start: { x: 100, y: 100 },
    end: { x: 300, y: 300 },
    width: 10,
    text: 'Line',
    color: 'blue',
  }),
];

// Type guard to check if a shape is a Circle
function isCircle(shape: Shape): shape is Circle {
  return (shape as Circle).radius !== undefined;
}

// Type guard to check if a shape is a Line
function isLine(shape: Shape): shape is Line {
  return (shape as Line).start !== undefined && (shape as Line).end !== undefined;
}

const handleClick = (event: MouseEvent) => {
  if (!canvas.value) return;

  const rect = canvas.value.getBoundingClientRect();
  const point = { x: event.clientX - rect.left, y: event.clientY - rect.top };
  const ctx = canvas.value.getContext('2d');
  if (!ctx) return;

  let shapeClicked = false;

  shapes.forEach((shape, index) => {
    if (shape.hitbox && shape.hitbox(point)) {
      console.log(`Shape ${index} clicked!`);
      shapeClicked = true;

      // Check if the shape is a Circle or a Line using type guards
      if (isCircle(shape)) {
        shape.color = 'green'; // Change color to green for Circle
      } else if (isLine(shape)) {
        shape.color = 'green'; // Change color to green for Line
      }
    }
  });

  // Redraw the canvas if a shape was clicked
  if (shapeClicked) {
    redraw(ctx);
  }
};

const redraw = (ctx: CanvasRenderingContext2D) => {
  ctx.clearRect(0, 0, canvas.value!.width, canvas.value!.height); // Clear the canvas
  shapes.forEach((shape) => shape.draw(ctx)); // Redraw all shapes
};

onMounted(() => {
  if (!canvas.value) return;

  const ctx = canvas.value.getContext('2d');
  if (!ctx) return;

  shapes.forEach((shape) => shape.draw(ctx));
});
</script>

<style>
canvas {
  border: 1px solid black;
  display: block;
  margin: 20px auto;
}
</style>
