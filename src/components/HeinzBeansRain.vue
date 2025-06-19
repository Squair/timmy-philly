<template>
  <div v-if="timer <= 0">
  <ConfettiExplosion :stageHeight="containerHeight" :stageWidth="containerWidth" />

  </div>
  <div class="beans-container" ref="container">
    <!-- Heinz branded background -->
    <div class="heinz-background">
      <div class="heinz-logo">
        <h1 v-if="timer > 0" class="brand-title">{{ timer }}</h1>
        <h1 v-else class="brand-title">Happy {{getAge}}th Birthday Timmy!</h1>
      </div>
    </div>


    <!-- Falling Beans -->
    <div
      v-for="bean in fallingBeans"
      :key="bean.id"
      class="falling-bean"
      :style="{
        top: '-50px',
        left: bean.startX + 'px',
        animationDuration: bean.duration + 's',
        animationDelay: bean.delay + 's',

        transform: `scale(${bean.scale})`,
        '--rotation-start': bean.rotationStart + 'deg',
        '--rotation-end': bean.rotationEnd + 'deg',
        '--fall-distance': bean.fallDistance

      }"
      @animationend="onBeanLanded(bean)"
    >
      <div class="bean-shape" :class="`bean-variant-${bean.variant}`">
        <div class="bean-sauce-coating"></div>
      </div>
    </div>

    <!-- Ground beans (accumulated) -->
    <div
      v-for="groundBean in groundBeans"
      :key="groundBean.id"
      class="ground-bean"
      :style="{
        left: groundBean.x + 'px',
        bottom: groundBean.bottom + 'px',
        transform: `rotate(${groundBean.rotation}deg) scale(${groundBean.scale})`,
        zIndex: groundBean.zIndex
      }"
    >
      <div class="bean-shape" :class="`bean-variant-${groundBean.variant}`">
        <div class="bean-sauce-coating"></div>
      </div>
    </div>

    <!-- Bean counter -->
    <div class="stats">
      <div class="heinz-stats-header">
        <strong>BEANZ COUNT</strong>
      </div>
      <div class="stat">
        <span class="stat-label">Falling:</span>
        <span class="stat-value">{{ fallingBeans.length }}</span>
      </div>
      <div class="stat">
        <span class="stat-label">Landed:</span>
        <span class="stat-value">{{ groundBeans.length }}</span>
      </div>
      <div class="stat">
        <span class="stat-label">Total:</span>
        <span class="stat-value">{{ totalBeans }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import ConfettiExplosion from "vue-confetti-explosion";

// Component state
const container = ref(null)
const containerWidth = ref(window.innerWidth)
const containerHeight = ref(window.innerHeight)
const fallingBeans = ref([])
const groundBeans = ref([])
const isRaining = ref(true)
const intensity = ref(5)
const beanIdCounter = ref(0)
const totalBeans = ref(0)

const timer = ref(10)

const timmyBirthday = new Date('2000-08-23');
const getAge = computed(() => {
  const today = new Date();
  const age = today.getFullYear() - timmyBirthday.getFullYear();
  const monthDiff = today.getMonth() - timmyBirthday.getMonth();
  if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < timmyBirthday.getDate())) {
    return age - 1;
  }
  return age;
});


// Animation intervals
let rainInterval = null
let cleanupInterval = null

const windowHeight = window.innerHeight;
const fallDistance = windowHeight + 50; // Extra buffer if needed

// Create a new falling bean
const createBean = () => {
  if (!container.value) return

  const containerWidth = container.value.offsetWidth
  
  const bean = {
    id: beanIdCounter.value++,
    startX: Math.random() * (containerWidth - 40), // Account for bean width
    scale: 0.7 + Math.random() * 0.6, // Random size between 0.7 and 1.3
    duration: 3 + Math.random() * 4, // Fall duration 3-7 seconds
    delay: Math.random() * 0.3, // Random delay up to 0.3s
    variant: Math.floor(Math.random() * 3) + 1, // 3 bean variants
    rotationStart: Math.random() * 360,
    rotationEnd: Math.random() * 360 + 360, // Full rotation plus random
    fallDistance: `${fallDistance}px`,
  }

  fallingBeans.value.push(bean)
  totalBeans.value++
}

// Handle bean landing 
const onBeanLanded = (bean) => {
  const index = fallingBeans.value.findIndex(b => b.id === bean.id)
  if (index !== -1) {
    fallingBeans.value.splice(index, 1)
    
    // Add to ground beans
    const groundBean = {
      id: `ground-${bean.id}`,
      x: bean.startX + (Math.random() - 0.5) * 30,
      bottom: Math.random() * 15,
      rotation: Math.random() * 360,
      scale: bean.scale * (0.9 + Math.random() * 0.2),
      variant: bean.variant,
      zIndex: Math.floor(Math.random() * 10) + 1
    }
    
    groundBeans.value.push(groundBean)
  }
}

// Start the rain
const startRain = () => {
  if (rainInterval) return
  
  const createBeans = () => {
    // Create beans based on intensity
    const beansToCreate = getAge.value
    for (let i = 0; i < beansToCreate; i++) {
      setTimeout(() => createBean(), i * 50) // Slight stagger
    }
  }
  createBeans()
  // Create beans at intervals based on intensity
  // const intervalTime = Math.max(200, 800 - (intensity.value * 60))
  // rainInterval = setInterval(createBeansBasedOnIntensity, intervalTime)
}

// Stop the rain
const stopRain = () => {
  if (rainInterval) {
    clearInterval(rainInterval)
    rainInterval = null
  }
}

// Clear all beans
const clearBeans = () => {
  fallingBeans.value = []
  groundBeans.value = []
  totalBeans.value = 0
}

// Cleanup ground beans periodically
const startCleanup = () => {
  cleanupInterval = setInterval(() => {
    if (groundBeans.value.length > 150) {
      groundBeans.value = groundBeans.value.slice(-150)
    }
  }, 10000)
}

// Lifecycle hooks
onMounted(() => {
  startCleanup()

  setInterval(() => {
    timer.value--
  }, 1000)
  
  setTimeout(() => {
    if (isRaining.value) {
      startRain()
    }
  }, 11000)

  const updateDimensions = () => {
    containerWidth.value = window.innerWidth;
    containerHeight.value = window.innerHeight;
  };
  window.addEventListener('resize', updateDimensions);
  updateDimensions();
})

onUnmounted(() => {
  stopRain()
  if (cleanupInterval) {
    clearInterval(cleanupInterval)
  }
})
</script>

<style scoped>
.beans-container {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: linear-gradient(135deg, #FF6B35 0%, #F7931E 50%, #FFD23F 100%);
}

.heinz-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: 
    radial-gradient(circle at 30% 40%, rgba(255, 255, 255, 0.1) 0%, transparent 50%),
    radial-gradient(circle at 70% 60%, rgba(255, 107, 53, 0.2) 0%, transparent 50%);
  pointer-events: none;
}

.heinz-logo {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
  opacity: 0.7;
  pointer-events: none;
}

.brand-title {
  font-size: 5rem;
  font-weight: 900;
  color: #FFFFFF;
  margin: 0;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  letter-spacing: 0.2em;
}

.brand-subtitle {
  font-size: 2rem;
  color: #FFFFFF;
  margin: 0;
  font-weight: 600;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
}

.intensity-control, .sauce-toggle {
  display: flex;
  align-items: center;
  gap: 12px;
  font-size: 14px;
  font-weight: 600;
  color: #FF6B35;
}

.slider {
  flex: 1;
  height: 8px;
  border-radius: 4px;
  background: linear-gradient(to right, #FFD23F, #FF6B35);
  outline: none;
  cursor: pointer;
}

.slider::-webkit-slider-thumb {
  appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #FFFFFF;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(255, 107, 53, 0.5);
  border: 2px solid #FF6B35;
}

.falling-bean {
  display: block;
  position: absolute;
  top: -50px;
  width: 20px;
  height: 30px;
  animation-name: fall;
  animation-timing-function: linear;
  animation-fill-mode: forwards;  
  -webkit-animation: fall 10s;
  -webkit-animation-timing-function: linear;
  -moz-animation: fall 10s;
  -moz-animation-timing-function: linear;
  -o-animation: fall 10s;
  -o-animation-timing-function: linear;
  z-index: 100;
}

.bean-shape {
  width: 100%;
  height: 100%;
  position: relative;
  animation: rotate linear infinite;
  animation-duration: inherit;
  background: linear-gradient(135deg, #D2B48C 0%, #CD853F 30%, #A0522D 70%, #8B4513 100%);
  border-radius: 45% 55% 40% 60% / 55% 45% 65% 35%;
  box-shadow: 
    inset -4px -6px 8px rgba(101, 67, 33, 0.4),
    inset 2px 3px 6px rgba(255, 228, 196, 0.6),
    0 3px 8px rgba(0, 0, 0, 0.3),
    0 1px 3px rgba(0, 0, 0, 0.2);
  transform-origin: center center;
}

/* Bean hilum (the "eye" where bean was attached to pod) */
.bean-shape::before {
  content: '';
  position: absolute;
  top: 30%;
  left: 15%;
  width: 25%;
  height: 8%;
  background: linear-gradient(90deg, rgba(101, 67, 33, 0.8) 0%, rgba(139, 69, 19, 0.6) 100%);
  border-radius: 50%;
  box-shadow: inset 0 1px 2px rgba(0, 0, 0, 0.4);
  transform: rotate(-15deg);
}

/* Bean surface texture highlight */
.bean-shape::after {
  content: '';
  position: absolute;
  top: 20%;
  right: 25%;
  width: 30%;
  height: 40%;
  background: radial-gradient(ellipse at 40% 30%, rgba(255, 228, 196, 0.4) 0%, transparent 60%);
  border-radius: 60% 40% 50% 50%;
  transform: rotate(25deg);
}

/* Different bean variants with realistic baked bean colors */
.bean-variant-1 {
  background: linear-gradient(135deg, #DEB887 0%, #D2B48C 25%, #CD853F 50%, #A0522D 75%, #8B4513 100%);
  border-radius: 42% 58% 38% 62% / 52% 48% 68% 32%;
}

.bean-variant-1::before {
  background: linear-gradient(90deg, rgba(139, 69, 19, 0.9) 0%, rgba(160, 82, 45, 0.7) 100%);
  top: 25%;
  left: 12%;
  width: 28%;
  height: 10%;
}

.bean-variant-2 {
  background: linear-gradient(135deg, #F5DEB3 0%, #DEB887 25%, #D2B48C 50%, #CD853F 75%, #A0522D 100%);
  border-radius: 48% 52% 42% 58% / 58% 42% 62% 38%;
}

.bean-variant-2::before {
  background: linear-gradient(90deg, rgba(160, 82, 45, 0.8) 0%, rgba(205, 133, 63, 0.6) 100%);
  top: 35%;
  left: 18%;
  width: 22%;
  height: 9%;
  transform: rotate(-20deg);
}

.bean-variant-3 {
  background: linear-gradient(135deg, #D2B48C 0%, #BC9A6A 25%, #A0522D 50%, #8B4513 75%, #654321 100%);
  border-radius: 46% 54% 36% 64% / 54% 46% 66% 34%;
}

.bean-variant-3::before {
  background: linear-gradient(90deg, rgba(101, 67, 33, 0.9) 0%, rgba(139, 69, 19, 0.7) 100%);
  top: 28%;
  left: 14%;
  width: 26%;
  height: 11%;
  transform: rotate(-10deg);
}

/* Enhanced sauce coating for more realistic appearance */
.bean-sauce-coating {
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: 
    radial-gradient(ellipse at 30% 20%, rgba(220, 38, 38, 0.8) 0%, transparent 40%),
    radial-gradient(ellipse at 70% 60%, rgba(185, 28, 28, 0.6) 0%, transparent 35%),
    radial-gradient(ellipse at 20% 80%, rgba(239, 68, 68, 0.7) 0%, transparent 30%),
    linear-gradient(135deg, rgba(220, 38, 38, 0.4) 0%, rgba(185, 28, 28, 0.5) 50%, rgba(153, 27, 27, 0.6) 100%);
  border-radius: inherit;
  animation: sauceGlisten 3s ease-in-out infinite alternate;
  box-shadow: 
    0 0 4px rgba(220, 38, 38, 0.6),
    inset 0 1px 2px rgba(255, 255, 255, 0.2);
}

/* Add sauce drips on individual beans */
.bean-sauce-coating::before {
  content: '';
  position: absolute;
  bottom: -3px;
  left: 20%;
  width: 4px;
  height: 8px;
  background: linear-gradient(180deg, rgba(220, 38, 38, 0.9) 0%, rgba(185, 28, 28, 0.7) 100%);
  border-radius: 0 0 50% 50%;
  animation: beanSauceDrip 4s ease-in-out infinite;
}

.bean-sauce-coating::after {
  content: '';
  position: absolute;
  bottom: -2px;
  right: 25%;
  width: 3px;
  height: 6px;
  background: linear-gradient(180deg, rgba(185, 28, 28, 0.8) 0%, rgba(153, 27, 27, 0.6) 100%);
  border-radius: 0 0 50% 50%;
  animation: beanSauceDrip 5s ease-in-out infinite reverse;
}

/* Ground beans get slightly different styling to show they've settled */
.ground-bean .bean-shape {
  box-shadow: 
    inset -3px -5px 6px rgba(101, 67, 33, 0.3),
    inset 2px 2px 4px rgba(255, 228, 196, 0.4),
    0 2px 6px rgba(0, 0, 0, 0.4),
    0 1px 2px rgba(0, 0, 0, 0.3);
}

.ground-bean .bean-sauce-coating {
  opacity: 0.9;
  background: 
    radial-gradient(ellipse at 40% 30%, rgba(220, 38, 38, 0.6) 0%, transparent 50%),
    linear-gradient(135deg, rgba(220, 38, 38, 0.3) 0%, rgba(185, 28, 28, 0.4) 100%);
}


/* Enhanced sauce glisten animation */
@keyframes sauceGlisten {
  0% {
    opacity: 0.4;
    filter: brightness(1);
  }
  50% {
    opacity: 0.7;
    filter: brightness(1.2);
  }
  100% {
    opacity: 0.5;
    filter: brightness(1.1);
  }
}

/* Add subtle bean texture animation */
@keyframes beanTexture {
  0%, 100% {
    filter: contrast(1) brightness(1);
  }
  50% {
    filter: contrast(1.05) brightness(1.02);
  }
}

/* Apply texture animation to beans */
.bean-shape {
  animation: rotate linear infinite, beanTexture 6s ease-in-out infinite;
}

.ground-bean {
  position: absolute;
  width: 16px;
  height: 24px;
  pointer-events: none;
}

.stats {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 1000;
  background: rgba(255, 255, 255, 0.95);
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
  backdrop-filter: blur(10px);
  min-width: 150px;
  border: 2px solid #FF6B35;
}

.heinz-stats-header {
  text-align: center;
  color: #FF6B35;
  font-size: 12px;
  font-weight: 800;
  margin-bottom: 10px;
  letter-spacing: 1px;
}

.stat {
  display: flex;
  justify-content: space-between;
  margin-bottom: 8px;
  font-size: 14px;
}

.stat:last-child {
  margin-bottom: 0;
  border-top: 2px solid #FF6B35;
  padding-top: 8px;
  font-weight: 700;
}

.stat-label {
  color: #666;
  font-weight: 600;
}

.stat-value {
  color: #FF6B35;
  font-weight: 700;
}

.heinz-can {
  position: absolute;
  bottom: 20px;
  left: 20px;
  width: 80px;
  height: 120px;
  z-index: 500;
}

.can-body {
  width: 100%;
  height: 100%;
  background: linear-gradient(180deg, #FF6B35 0%, #DC2626 100%);
  border-radius: 8px 8px 12px 12px;
  position: relative;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
}

.can-label {
  position: absolute;
  top: 15px;
  left: 5px;
  right: 5px;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 6px;
  padding: 8px 4px;
  text-align: center;
}

.heinz-text {
  font-size: 14px;
  font-weight: 900;
  color: #FF6B35;
  letter-spacing: 1px;
}

.beans-text {
  font-size: 8px;
  font-weight: 700;
  color: #333;
  margin: 2px 0;
}

.tagline {
  font-size: 6px;
  color: #666;
  font-weight: 500;
}

/* Animations */
@keyframes fall {
  from {
    transform: translateY(0) rotate(var(--rotation-start));
  }
  to {
    transform: translateY(var(--fall-distance)) rotate(var(--rotation-end));
  }
}

@keyframes rotate {
  from {
    transform: rotate(var(--rotation-start));
  }
  to {
    transform: rotate(var(--rotation-end));
  }
}

@keyframes particleFly {
  0% {
    transform: translate(-50%, -50%) rotate(var(--particle-angle)) translateY(0);
    opacity: 1;
  }
  100% {
    transform: translate(-50%, -50%) rotate(var(--particle-angle)) translateY(-30px);
    opacity: 0;
  }
}


@keyframes sauceGlisten {
  0% {
    opacity: 0.3;
  }
  100% {
    opacity: 0.6;
  }
}

/* Responsive design */
@media (max-width: 768px) {
  .brand-title {
    font-size: 4rem;
  }
  
  .brand-subtitle {
    font-size: 1.2rem;
  }
  
  .controls, .stats {
    padding: 15px;
  }
  
  .heinz-can {
    width: 60px;
    height: 90px;
  }
}
</style>