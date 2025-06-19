<template>
  <div v-if="timer <= 0">
  <ConfettiExplosion v-if="eatenBeans.length === getAge" :stageHeight="containerHeight" :stageWidth="containerWidth" />

  </div>
  <div class="beans-container" ref="container">
    <!-- Heinz branded background -->
    <div class="heinz-background">
      <div class="heinz-logo">
        <h1 v-if="timer > 0" class="brand-title">{{ timer }}</h1>
        <h1 v-else-if="eatenBeans.length === getAge" class="brand-title">Happy {{getAge}}th Birthday Timmy!</h1>
      </div>
    </div>


    <!-- Falling Beans -->
    <div
      v-for="bean in fallingBeans"
      :key="bean.id"
      class="falling-bean"
      :style="{
        top: bean.startY + 'px',
        left: bean.startX + 'px',
      }"
      @click="eatBean(bean.id)"
    >
      <div class="bean-shape" :class="`bean-variant-${bean.variant}`">
        <div class="bean-sauce-coating"></div>
      </div>
    </div>

    <!-- Bean counter -->
    <div class="stats">
      <div class="heinz-stats-header">
        <strong>BEANZ COUNT</strong>
      </div>
      <div class="stat">
        <span class="stat-label">Ready to eat:</span>
        <span class="stat-value">{{ fallingBeans.length }}</span>
      </div>
      <div class="stat">
        <span class="stat-label">Eaten:</span>
        <span class="stat-value">{{ eatenBeans.length }}</span>
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
const eatenBeans = ref([])
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

const eatBean = (id) => {
  const index = fallingBeans.value.findIndex(bean => bean.id === id)
  if (index !== -1) {
    const bean = fallingBeans.value[index]
    eatenBeans.value.push(bean)
    fallingBeans.value.splice(index, 1)
  }
}


// Animation intervals
let rainInterval = null
let cleanupInterval = null

const windowHeight = window.innerHeight;
const fallDistance = windowHeight + 50; // Extra buffer if needed

// Create a new falling bean
const createBean = () => {
  if (!container.value) return

  const containerWidth = container.value.offsetWidth
  const containerHeight = container.value.offsetHeight
  console.log(containerHeight);
  
  const bean = {
    id: beanIdCounter.value++,
    startX: Math.random() * (containerWidth - 40) + 20, // Account for bean width
    startY: Math.random() * (containerHeight - 40) + 20, // Account for bean width
    scale: 0.7 + Math.random() * 0.6, // Random size between 0.7 and 1.3
    duration: 3 + Math.random() * 4, // Fall duration 3-7 seconds
    delay: Math.random() * 0.3, // Random delay up to 0.3s
    variant: Math.floor(Math.random() * 3) + 1, // 3 bean variants
  }

  fallingBeans.value.push(bean)
  totalBeans.value++
}

// Start the rain
const startRain = () => {
  if (rainInterval) return
  
  const createBeans = () => {
    // Create beans based on intensity
    const beansToCreate = getAge.value
    for (let i = 0; i < beansToCreate; i++) {
      setTimeout(() => createBean(), i * 1000) // Slight stagger
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
  totalBeans.value = 0
}

// Lifecycle hooks
onMounted(() => {
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
  height: 100dvh;
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

.falling-bean {
  position: absolute;
  width: 20px;
  height: 30px;
  z-index: 1001;
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
  0% {
    top: 0px;
    transform: rotate(var(--rotation-start));
  }
  100% {
    top: var(--fall-distance);
    transform: rotate(var(--rotation-end));
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
  
  .stats {
    padding: 15px;
    z-index: 1;
  }

}
</style>