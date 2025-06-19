<template>
  <HeinzBeansRainNew v-if="beanCount === 5" />
  <div v-else class="min-h-screen bg-cover bg-[url(/img/background.jpg)] flex items-center justify-center p-4">
    <div class="w-full max-w-4xl bg-white/95 backdrop-blur-sm shadow-2xl rounded-lg overflow-hidden">
      <div class="grid md:grid-cols-2 gap-0">
        <!-- Left Side - Main Info -->
        <div class="p-8 md:p-12 space-y-6 flex flex-col gap-8">
          <div class="space-y-4">
            <div class="flex items-center gap-3">
              <div class="p-2 bg-slate-900 rounded-lg">
                <AudioWaveformIcon class="w-6 h-6 text-white" />
              </div>
              <div>
                <h1 class="text-3xl font-bold text-slate-900">Timmy Phillips</h1>
                <p class="text-lg text-slate-600">Professional Sound Engineer</p>
              </div>
            </div>

            <p class="text-slate-700 leading-relaxed">
              Passionate audio professional with 5+ years of experience in recording, mixing, and mastering.
              Specializing in live sound.
            </p>
          </div>

          <div class="border-t border-slate-200"></div>

          <!-- Services -->
          <div class="space-y-6 flex flex-col gap-5">
            <h2 class="text-xl font-semibold text-slate-900 flex items-center gap-2">
              <Music class="w-5 h-5" />
              Services
            </h2>
            <div class="grid grid-cols-2 gap-3">
              <div class="flex items-center gap-2 text-sm text-slate-700">
                <Mic class="w-4 h-4 text-slate-500" />
                Recording
              </div>
              <div class="flex items-center gap-2 text-sm text-slate-700">
                <Headphones class="w-4 h-4 text-slate-500" />
                Mixing
              </div>
              <div class="flex items-center gap-2 text-sm text-slate-700">
                <Volume2 class="w-4 h-4 text-slate-500" />
                Mastering
              </div>
              <div class="flex items-center gap-2 text-sm text-slate-700">
                <Radio class="w-4 h-4 text-slate-500" />
                Live Sound
              </div>
            </div>
          </div>

          <!-- Specialties -->
          <div class="space-y-3">
            <h3 class="font-medium text-slate-900">Specialties</h3>
            <div class="flex flex-wrap gap-2">
              <span 
                v-for="specialty in specialties" 
                :key="specialty"
                class="inline-flex items-center rounded-md bg-slate-100 px-2 py-1 text-xs font-medium text-slate-700 ring-1 ring-inset ring-slate-600/10"
                @click="checkBeans(specialty)"
              >
                {{ specialty }}
              </span>
            </div>
          </div>
        </div>

        <!-- Right Side - Contact & Social -->
        <div class="bg-slate-50 p-8 md:p-12 space-y-6">
          <div class="space-y-4">
            <h2 class="text-xl font-semibold text-slate-900">Get In Touch</h2>

            <div class="space-y-4">
              <div class="flex items-center gap-3 text-slate-700">
                <div class="p-2 bg-white rounded-lg shadow-sm">
                  <Mail class="w-4 h-4" />
                </div>
                <div>
                  <p class="text-sm text-slate-500">Email</p>
                  <p class="font-medium">timmyphilly@some-domain.com</p>
                </div>
              </div>

              <div class="flex items-center gap-3 text-slate-700">
                <div class="p-2 bg-white rounded-lg shadow-sm">
                  <Phone class="w-4 h-4" />
                </div>
                <div>
                  <p class="text-sm text-slate-500">Phone</p>
                  <p class="font-medium">+44 7463 818156</p>
                </div>
              </div>

              <div class="flex items-center gap-3 text-slate-700">
                <div class="p-2 bg-white rounded-lg shadow-sm">
                  <MapPin class="w-4 h-4" />
                </div>
                <div>
                  <p class="text-sm text-slate-500">Location</p>
                  <p class="font-medium">Bristol, UK</p>
                </div>
              </div>
            </div>
          </div>

          <div class="border-t border-slate-200"></div>

          <!-- Social Links -->
          <div class="space-y-4">
            <h3 class="font-medium text-slate-900">Connect</h3>
            <div class="flex gap-3">
              <button 
                v-for="social in socialLinks" 
                :key="social.name"
                @click="openLink(social.url)"
                class="inline-flex items-center justify-center rounded-full border border-slate-200 bg-white px-3 py-2 text-sm font-semibold text-slate-900 shadow-sm hover:bg-slate-50 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-slate-600 w-10 h-10"
              >
                <component :is="social.icon" class="w-4 h-4" />
              </button>
            </div>
          </div>

          <!-- Studio Info -->
          <div class="bg-white p-4 rounded-lg shadow-sm">
            <h3 class="font-medium text-slate-900 mb-2">Studio Equipment</h3>
            <ul class="text-sm text-slate-600 space-y-1">
              <li v-for="equipment in studioEquipment" :key="equipment">• {{ equipment }}</li>
            </ul>
          </div>

          <!-- CTA -->
          <div class="pt-4">
            <button 
              @click="startProject"
              class="w-full inline-flex items-center justify-center rounded-md bg-slate-900 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-slate-800 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-slate-600"
            >
              <Mail class="w-4 h-4 mr-2" />
              Start Your Project
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import {
  Mail,
  Phone,
  MapPin,
  Linkedin,
  Instagram,
  Globe,
  Headphones,
  Mic,
  Music,
  Radio,
  Volume2,
  AudioWaveformIcon,
} from 'lucide-vue-next'
import HeinzBeansRainNew from './HeinzBeansRainNew.vue'

const specialties = ref([
  'Rock & Metal',
  'Electronic',
  'Jazz',
  'Beanz'
])

const socialLinks = ref([
  { name: 'LinkedIn', icon: Linkedin, url: '' },
  { name: 'Instagram', icon: Instagram, url: '' },
  { name: 'Website', icon: Globe, url: '' }
])

const studioEquipment = ref([
  'Pro Tools HDX System',
  'Neumann & AKG Microphones',
  'SSL Console',
  'Genelec Monitoring'
])

const beanCount = ref(0)

const openLink = (url) => {
  window.open(url, '_blank')
}

const startProject = () => {
  window.location.href = 'mailto:georgesquair@gmail.com?subject=New Project Inquiry'
}

const checkBeans = (specialty) => {
  if (specialty === 'Beanz') {
    beanCount.value++
  }
}

</script>

<style scoped>
/* Additional custom styles if needed */
.backdrop-blur-sm {
  backdrop-filter: blur(4px);
}
</style>