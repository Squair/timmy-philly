<template>
  <div v-if="timer <= 0">
    <ConfettiExplosion :particleCount="200" :stageHeight="1200" :force="0.3" />
    <ConfettiExplosion :particleCount="200" :stageHeight="1200" :stageWidth="2500":force="0.3" />
    <ConfettiExplosion :particleCount="200" :stageHeight="1200" :stageWidth="3500":force="0.3" />
    <ConfettiExplosion :particleCount="200" :stageHeight="1200" :stageWidth="4500" :force="0.3" />

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
        left: bean.startX + 'px',
        animationDuration: bean.duration + 's',
        animationDelay: bean.delay + 's',
        transform: `scale(${bean.scale})`,
        '--rotation-start': bean.rotationStart + 'deg',
        '--rotation-end': bean.rotationEnd + 'deg'
      }"
      @animationend="onBeanLanded(bean)"
    >
      <div class="bean-shape" :class="`bean-variant-${bean.variant}`"></div>
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
      <div class="bean-shape" :class="`bean-variant-${groundBean.variant}`"></div>
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

    <!-- Heinz can (decorative) -->
    <div class="heinz-can">
      <div class="can-body">
        <div class="can-label">
          <div class="heinz-text">HEINZ</div>
          <div class="beans-text">BAKED BEANS</div>
          <div class="tagline">in Tomato Sauce</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import ConfettiExplosion from "vue-confetti-explosion";

// Component state
const container = ref(null)
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
  }

  fallingBeans.value.push(bean)
  totalBeans.value++
}

// Handle bean landing
const onBeanLanded = (bean) => {
  // Remove from falling beans
  const index = fallingBeans.value.findIndex(b => b.id === bean.id)
  if (index !== -1) {
    fallingBeans.value.splice(index, 1)
    
    // Add to ground beans
    const groundBean = {
      id: `ground-${bean.id}`,
      x: bean.startX + (Math.random() - 0.5) * 30, // Slight horizontal scatter
      bottom: Math.random() * 15, // Random ground level
      rotation: Math.random() * 360,
      scale: bean.scale * (0.9 + Math.random() * 0.2), // Slightly smaller on ground
      variant: bean.variant,
      zIndex: Math.floor(Math.random() * 10) + 1
    }
    
    groundBeans.value.push(groundBean)
  }
}

// Toggle rain on/off
const toggleRain = () => {
  isRaining.value = !isRaining.value
  
  if (isRaining.value) {
    startRain()
  } else {
    stopRain()
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

.controls {
  position: absolute;
  top: 20px;
  left: 20px;
  z-index: 1000;
  display: flex;
  flex-direction: column;
  gap: 12px;
  background: rgba(255, 255, 255, 0.95);
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
  backdrop-filter: blur(10px);
  border: 2px solid #FF6B35;
}

.control-btn {
  padding: 12px 24px;
  border: none;
  border-radius: 25px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 14px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.control-btn.start {
  background: linear-gradient(45deg, #FF6B35, #F7931E);
  color: white;
  box-shadow: 0 4px 15px rgba(255, 107, 53, 0.4);
}

.control-btn.start:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(255, 107, 53, 0.6);
}

.control-btn.stop {
  background: linear-gradient(45deg, #DC2626, #EF4444);
  color: white;
  box-shadow: 0 4px 15px rgba(220, 38, 38, 0.4);
}

.control-btn.stop:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(220, 38, 38, 0.6);
}

.control-btn.clear {
  background: linear-gradient(45deg, #6366F1, #8B5CF6);
  color: white;
  box-shadow: 0 4px 15px rgba(99, 102, 241, 0.4);
}

.control-btn.clear:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(99, 102, 241, 0.6);
}

.intensity-control {
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
  position: absolute;
  top: -50px;
  width: 20px;
  height: 30px;
  animation: fall linear forwards;
  z-index: 100;
}

.bean-shape {
  width: 100%;
  height: 100%;
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
  position: relative;
  animation: rotate linear infinite;
  animation-duration: inherit;
  box-shadow: 
    inset -3px -3px 6px rgba(0, 0, 0, 0.3),
    inset 2px 2px 4px rgba(255, 255, 255, 0.3),
    0 4px 8px rgba(0, 0, 0, 0.2);
}

.bean-variant-1 {
  background: linear-gradient(135deg, #8B4513 0%, #A0522D 50%, #CD853F 100%);
}

.bean-variant-2 {
  background: linear-gradient(135deg, #A0522D 0%, #CD853F 50%, #DEB887 100%);
}

.bean-variant-3 {
  background: linear-gradient(135deg, #654321 0%, #8B4513 50%, #A0522D 100%);
}

.bean-shape::before {
  content: '';
  position: absolute;
  top: 20%;
  left: 20%;
  width: 60%;
  height: 60%;
  background: radial-gradient(ellipse at 30% 30%, rgba(255, 255, 255, 0.4) 0%, transparent 70%);
  border-radius: 50%;
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

@keyframes fall {
  from {
    transform: translateY(0) rotate(var(--rotation-start));
  }
  to {
    transform: translateY(calc(100vh + 100px)) rotate(var(--rotation-end));
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