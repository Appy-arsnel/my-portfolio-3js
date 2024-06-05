<template>
  <canvas ref="canvas" />
</template>

<script>
import { onMounted, onUnmounted, ref } from 'vue';

export default {
  setup() {
    const canvas = ref(null);
    let animationFrameId = null;

    const initCanvas = () => {
      const ctx = canvas.value.getContext('2d');
      canvas.value.width = window.innerWidth;
      canvas.value.height = window.innerHeight;

      const raindrops = [];

      const animate = () => {
        ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);

        raindrops.forEach((raindrop) => {
          raindrop.y += raindrop.speed;
          ctx.beginPath();
          ctx.arc(raindrop.x, raindrop.y, raindrop.size, 0, 2 * Math.PI);
          ctx.fillStyle = 'rgba(0, 0, 255, 0.5)';
          ctx.fill();

          if (raindrop.y > canvas.value.height) {
            raindrop.y = 0;
            raindrop.x = Math.random() * canvas.value.width;
          }
        });

        animationFrameId = requestAnimationFrame(animate);
      };

      for (let i = 0; i < 100; i++) {
        raindrops.push({
          x: Math.random() * canvas.value.width,
          y: Math.random() * canvas.value.height,
          size: Math.random() * 5 + 1,
          speed: Math.random() * 5 + 1,
        });
      }

      animate();
    };

    onMounted(() => {
      initCanvas();
    });

    onUnmounted(() => {
      cancelAnimationFrame(animationFrameId);
    });

    return {
      canvas,
    };
  },
};
</script>