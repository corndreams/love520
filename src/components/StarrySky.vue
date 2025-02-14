<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue';

const canvasRef = ref<HTMLCanvasElement | null>(null);
let ctx: CanvasRenderingContext2D | null = null;
let animationId: number;

interface Star {
  x: number;
  y: number;
  size: number;
  speed: number;
  brightness: number;
}

const stars: Star[] = [];
const STAR_COUNT = 200;

function initStars() {
  const canvas = canvasRef.value!;
  for (let i = 0; i < STAR_COUNT; i++) {
    stars.push({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      size: Math.random() * 2 + 0.5,
      speed: Math.random() * 0.3 + 0.1,
      brightness: Math.random()
    });
  }
}

function drawStars() {
  if (!ctx || !canvasRef.value) return;
  
  ctx.fillStyle = 'rgba(0, 0, 0, 0.1)';
  ctx.fillRect(0, 0, canvasRef.value.width, canvasRef.value.height);
  
  stars.forEach(star => {
    const brightness = 0.5 + Math.sin(Date.now() * 0.001 + star.brightness * 10) * 0.5;
    ctx!.fillStyle = `rgba(255, 255, 255, ${brightness})`;
    ctx!.beginPath();
    ctx!.arc(star.x, star.y, star.size, 0, Math.PI * 2);
    ctx!.fill();
    
    star.y -= star.speed;
    if (star.y < -10) {
      star.y = canvasRef.value!.height + 10;
      star.x = Math.random() * canvasRef.value!.width;
    }
  });
  
  animationId = requestAnimationFrame(drawStars);
}

function resizeCanvas() {
  if (!canvasRef.value) return;
  canvasRef.value.width = window.innerWidth;
  canvasRef.value.height = window.innerHeight;
}

onMounted(() => {
  if (!canvasRef.value) return;
  ctx = canvasRef.value.getContext('2d');
  resizeCanvas();
  initStars();
  drawStars();
  window.addEventListener('resize', resizeCanvas);
});

onUnmounted(() => {
  cancelAnimationFrame(animationId);
  window.removeEventListener('resize', resizeCanvas);
});
</script>

<template>
  <canvas ref="canvasRef" class="starry-sky"></canvas>
</template>

<style scoped>
.starry-sky {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  background: linear-gradient(to bottom, #0a0a2a, #1a1a4a);
}
</style>