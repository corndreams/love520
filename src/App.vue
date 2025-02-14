<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { gsap } from 'gsap';
import { Howl } from 'howler';
import StarrySky from './components/StarrySky.vue';
import PhotoWall from './components/PhotoWall.vue';
import ThreeDLetter from './components/ThreeDLetter.vue';

const currentStep = ref(0);
const showConstellationModal = ref(false);
const name = ref('亲爱的');

const bgm = new Howl({
  src: ['./src/assets/love.mp3'],
  loop: true,
  volume: 0.5
});

const clockSound = new Howl({
  src: ['./src/assets/clock.mp3'],
  volume: 0.3
});

onMounted(() => {
  clockSound.play();
  setTimeout(() => {
    clockSound.fade(0.3, 0, 1000);
    setTimeout(() => {
      clockSound.stop();
      bgm.play();
    }, 1000);
  }, 5000);

  gsap.from('.title', {
    opacity: 0,
    y: 30,
    duration: 2,
    delay: 1
  });

  createParticles();
});

const particles: HTMLDivElement[] = [];

function createParticles() {
  const container = document.querySelector('.particle-container');
  if (!container) return;

  for (let i = 0; i < 50; i++) {
    const particle = document.createElement('div');
    particle.className = 'particle';
    particle.style.left = Math.random() * 100 + 'vw';
    particle.style.animationDelay = Math.random() * 5 + 's';
    container.appendChild(particle);
    particles.push(particle);
  }
}

function nextStep() {
  currentStep.value++;
  if (currentStep.value === 1) {
    showConstellation();
  }
}

function showConstellation() {
  showConstellationModal.value = true;
  gsap.from('.constellation-modal', {
    scale: 0,
    opacity: 0,
    duration: 1,
    ease: 'back.out'
  });
}
</script>

<template>
  <!-- <img src="./assets/yourImg.png" alt=""> -->
  <div class="app">
    <StarrySky />
    <div class="particle-container"></div>
    
    <div class="content" v-if="currentStep === 0">
      <h1 class="title">{{ name }}</h1>
      <div class="heart-container" @click="nextStep">
        <div class="heart"></div>
      </div>
      <p class="hint">点击爱心开启浪漫之旅</p>
    </div>

    <div v-if="showConstellationModal && currentStep === 1" class="constellation-modal">
      <div class="constellation">
        <svg viewBox="0 0 200 200" class="constellation-svg">
          <path class="constellation-line" d="M50,50 L100,100 L150,50 L100,150" />
          <circle cx="50" cy="50" r="3" class="star" />
          <circle cx="100" cy="100" r="3" class="star" />
          <circle cx="150" cy="50" r="3" class="star" />
          <circle cx="100" cy="150" r="3" class="star" />
        </svg>
      </div>
      <div class="message">
        <h2>我的星星</h2>
        <p>在这浩瀚的星空下</p>
        <p>我想对你说</p>
        <p>你就是我生命中最亮的那颗星</p>
        <p>我喜欢你</p>
        <div class="buttons">
          <button class="btn accept" @click="nextStep">接受我的心意</button>
          <button class="btn think" @click="nextStep">让我想想</button>
        </div>
      </div>
    </div>

    <Transition name="fade">
      <PhotoWall v-if="currentStep === 2" @click="nextStep" />
    </Transition>

    <Transition name="fade">
      <ThreeDLetter v-if="currentStep === 3" />
    </Transition>
  </div>
</template>

<style>
.app {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  overflow: hidden;
  position: fixed;
  top: 0;
  left: 0;
  background: transparent;
}

.content {
  text-align: center;
  z-index: 1;
}

.title {
  font-size: 3em;
  margin-bottom: 2em;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
  color: #fff;
}

.heart-container {
  cursor: pointer;
  transition: transform 0.3s ease;
  width: 100px;
  height: 100px;
  margin: 0 auto;
  position: relative;
}

.heart-container:hover {
  transform: scale(1.1);
}

.heart {
  width: 50px;
  height: 50px;
  background: #ff3366;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) rotate(45deg);
  animation: pulse 1.5s ease infinite;
  box-shadow: 0 0 20px rgba(255, 51, 102, 0.5);
}

.heart::before,
.heart::after {
  content: '';
  width: 50px;
  height: 50px;
  background: #ff3366;
  border-radius: 50%;
  position: absolute;
  box-shadow: 0 0 20px rgba(255, 51, 102, 0.5);
}

.heart::before {
  left: -25px;
  top: 0;
}

.heart::after {
  top: -25px;
  left: 0;
}

.hint {
  margin-top: 2em;
  font-size: 1.2em;
  opacity: 0.8;
  color: #fff;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.3);
}

.particle-container {
  position: fixed;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.particle {
  position: absolute;
  width: 2px;
  height: 2px;
  background: #fff;
  border-radius: 50%;
  animation: fall 5s linear infinite;
  box-shadow: 0 0 3px #fff;
}

.constellation-modal {
  background: rgba(0, 0, 0, 0.85);
  padding: 3em;
  border-radius: 20px;
  text-align: center;
  max-width: 600px;
  margin: 2em;
  box-shadow: 0 0 30px rgba(255, 51, 102, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.1);
  z-index: 2;
}

.constellation {
  margin-bottom: 2em;
}

.constellation-svg {
  width: 200px;
  height: 200px;
}

.constellation-line {
  fill: none;
  stroke: #fff;
  stroke-width: 1;
  stroke-dasharray: 300;
  stroke-dashoffset: 300;
  animation: drawLine 3s ease forwards;
  filter: drop-shadow(0 0 2px rgba(255, 255, 255, 0.5));
}

.star {
  fill: #fff;
  filter: drop-shadow(0 0 3px rgba(255, 255, 255, 0.8));
  animation: twinkle 1.5s ease infinite alternate;
}

.message h2 {
  color: #ff3366;
  font-size: 2em;
  margin-bottom: 1em;
  text-shadow: 0 0 10px rgba(255, 51, 102, 0.3);
}

.message p {
  margin: 0.8em 0;
  font-size: 1.2em;
  line-height: 1.6;
  color: #fff;
}

.btn {
  background: transparent;
  border: 2px solid #fff;
  color: #fff;
  padding: 1em 2em;
  margin: 1em;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 1.1em;
  text-shadow: 0 0 5px rgba(255, 255, 255, 0.3);
}

.btn:hover {
  background: #fff;
  color: #000;
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(255, 255, 255, 0.2);
}

.btn.accept {
  border-color: #ff3366;
  color: #ff3366;
  background: rgba(255, 51, 102, 0.1);
}

.btn.accept:hover {
  background: #ff3366;
  color: #fff;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

@keyframes pulse {
  0% { transform: translate(-50%, -50%) rotate(45deg) scale(1); }
  50% { transform: translate(-50%, -50%) rotate(45deg) scale(1.1); }
  100% { transform: translate(-50%, -50%) rotate(45deg) scale(1); }
}

@keyframes fall {
  0% { 
    transform: translateY(-100vh) rotate(0deg); 
    opacity: 1;
  }
  100% { 
    transform: translateY(100vh) rotate(360deg); 
    opacity: 0;
  }
}

@keyframes drawLine {
  to { stroke-dashoffset: 0; }
}

@keyframes twinkle {
  from { opacity: 0.3; }
  to { opacity: 1; }
}
</style>