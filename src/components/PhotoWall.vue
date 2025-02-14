<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { gsap } from 'gsap';

const photos = [
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png',
  './src/assets/yourImg.png'
];

const currentPhotoIndex = ref(0);
const showingPhoto = ref(true);
console.log(showingPhoto);

onMounted(() => {
  photos.forEach((_, index) => {
    setTimeout(() => {
      currentPhotoIndex.value = index;
      showCenterPhoto(index);
    }, index * 2000);
  });
});

function showCenterPhoto(index: number) {
  const photo = document.querySelector(`.photo-${index}`);
  if (!photo) return;

  gsap.fromTo(photo, 
    { 
      scale: 0,
      opacity: 0,
      x: 0,
      y: 0,
      rotation: -360
    },
    {
      scale: 1.5,
      opacity: 1,
      duration: 1,
      ease: 'back.out(1.7)',
      onComplete: () => {
        gsap.to(photo, {
          scale: 1,
          x: 0,
          y: 0,
          duration: 1,
          delay: 0.5,
          ease: 'power2.inOut'
        });
      }
    }
  );
}
</script>

<template>
  <div class="photo-wall">
    <div v-for="(photo, index) in photos" 
         :key="index" 
         class="photo-container">
      <div :class="['photo', `photo-${index}`]"
           :style="{ opacity: index > currentPhotoIndex ? 0 : 1 }">
        <img :src="photo" :alt="'Photo ' + (index + 1)" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.photo-wall {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  padding: 20px;
  max-width: 1600px;
  margin: 0 auto;
  height: 100vh;
  align-items: center;
}

.photo-container {
  aspect-ratio: 4/3;
  perspective: 1000px;
  position: relative;
}

.photo {
  width: 100%;
  height: 100%;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 0.6s ease;
  opacity: 0;
}

.photo:hover {
  transform: rotateY(10deg) rotateX(10deg);
  z-index: 10;
}

.photo img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 10px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
}

.photo::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(45deg, rgba(255, 51, 102, 0.2), transparent);
  border-radius: 10px;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.photo:hover::after {
  opacity: 1;
}
</style>