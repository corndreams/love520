<script setup lang="ts">
import { onMounted, onBeforeUnmount, ref } from 'vue';
import * as THREE from 'three';
import { gsap } from 'gsap';

const isOpen = ref(false);
const letterContent = ref('');

const init = () => {
  const container = document.querySelector('.letter-container');
  if (!container) return;

  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
  const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
  
  renderer.setSize(container.clientWidth, container.clientHeight);
  container.appendChild(renderer.domElement);

  // 创建信封
  const envelopeGeometry = new THREE.BoxGeometry(4, 3, 0.1);
  const letterMaterial = new THREE.MeshPhongMaterial({
    color: 0xff3366,
    specular: 0xffffff,
    shininess: 100,
    side: THREE.DoubleSide
  });

  // 创建信封的各个面
  const envelope = new THREE.Group();
  
  // 主体
  const main = new THREE.Mesh(envelopeGeometry, letterMaterial);
  envelope.add(main);

  // 信封翻盖
  const flapGeometry = new THREE.BufferGeometry();
  const vertices = new Float32Array([
    -2, 1.5, 0,   // 左下
    2, 1.5, 0,    // 右下
    0, 2.5, 0     // 顶点
  ]);
  flapGeometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));
  const flap = new THREE.Mesh(flapGeometry, letterMaterial);
  flap.position.z = 0.05;
  envelope.add(flap);

  // 添加花纹
  const pattern = new THREE.Mesh(
    new THREE.TorusKnotGeometry(0.3, 0.1, 64, 8),
    new THREE.MeshPhongMaterial({ color: 0xffd700 })
  );
  pattern.position.z = 0.06;
  envelope.add(pattern);

  scene.add(envelope);

  // 添加灯光
  const light1 = new THREE.DirectionalLight(0xffffff, 1);
  light1.position.set(1, 1, 1);
  scene.add(light1);

  const light2 = new THREE.PointLight(0xff3366, 1);
  light2.position.set(-2, 2, 4);
  scene.add(light2);

  const ambientLight = new THREE.AmbientLight(0x404040);
  scene.add(ambientLight);

  camera.position.z = 7;

  // 动画
  let isAnimating = false;
  const animate = () => {
    requestAnimationFrame(animate);
    if (!isOpen.value) {
      envelope.rotation.y += 0.005;
    }
    renderer.render(scene, camera);
  };

  const openLetter = () => {
    if (isAnimating) return;
    isAnimating = true;
    
    if (!isOpen.value) {
      gsap.to(envelope.rotation, {
        x: Math.PI / 2,
        duration: 1.5,
        ease: 'power2.inOut',
        onComplete: () => {
          isOpen.value = true;
          isAnimating = false;
          letterContent.value = `
亲爱的：

时光飞逝，我们相识已有许久
每一天与你在一起的点点滴滴
都让我感到无比珍贵
你的笑容是我生命中最美的风景
你的声音是我耳边最动听的音乐

今天，我想对你说
我爱你
不是一时冲动，而是深思熟虑的选择
愿意用我的一生去爱你、保护你
陪你看遍世间美景
与你共度余生每一个春秋

你愿意成为我的另一半吗？

永远爱你的我
          `;
        }
      });
    } else {
      gsap.to(envelope.rotation, {
        x: 0,
        duration: 1.5,
        ease: 'power2.inOut',
        onComplete: () => {
          isOpen.value = false;
          isAnimating = false;
          letterContent.value = '';
        }
      });
    }
  };

  container.addEventListener('click', openLetter);
  animate();

  // 清理函数
  return () => {
    container.removeEventListener('click', openLetter);
    renderer.dispose();
    container.removeChild(renderer.domElement);
  };
};

onMounted(() => {
  const cleanup = init();
  onBeforeUnmount(() => cleanup?.());
});
</script>

<template>
  <div class="letter-container">
    <div class="letter-content" v-if="isOpen" v-html="letterContent.split('\n').join('<br>')"></div>
    <div class="letter-hint" v-if="!isOpen">点击打开情书</div>
  </div>
</template>

<style scoped>
.letter-container {
  width: 100%;
  height: 100vh;
  position: relative;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}

.letter-content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: rgba(255, 255, 255, 0.95);
  padding: 2rem;
  border-radius: 15px;
  max-width: 600px;
  width: 90%;
  color: #333;
  font-size: 1.1rem;
  line-height: 1.8;
  white-space: pre-line;
  box-shadow: 0 0 30px rgba(255, 51, 102, 0.3);
  animation: fadeIn 1s ease;
}

.letter-hint {
  position: absolute;
  bottom: 2rem;
  left: 50%;
  transform: translateX(-50%);
  color: #fff;
  font-size: 1.2rem;
  text-shadow: 0 0 10px rgba(255, 51, 102, 0.5);
  animation: pulse 1.5s ease infinite;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translate(-50%, -40%); }
  to { opacity: 1; transform: translate(-50%, -50%); }
}

@keyframes pulse {
  0% { opacity: 0.5; transform: translateX(-50%) scale(0.95); }
  50% { opacity: 1; transform: translateX(-50%) scale(1.05); }
  100% { opacity: 0.5; transform: translateX(-50%) scale(0.95); }
}
</style>