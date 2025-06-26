<template>
  <HeinzBeansRain v-if="gameStarted" />
  <div
    v-else
    class="min-h-screen bg-contain bg-[url(/img/background.png)] flex items-center justify-center p-6"
  >
    <div
      class="w-full max-w-4xl bg-white/95 backdrop-blur-sm shadow-2xl rounded-lg overflow-hidden"
    >
      <div class="grid md:grid-cols-2 gap-0">
        <!-- Left Side - Main Info -->
        <div class="p-8 md:p-12 space-y-6 flex flex-col gap-3">
          <div class="space-y-4 flex flex-col gap-5">
            <div class="flex justify-between gap-3">
              <div class="flex flex-col justify-center">
                <h1 class="text-2xl text-nowrap font-bold text-slate-900">
                  Timmy Phillips
                </h1>
                <p class="text-lg  text-slate-600">
                  Professional Live Sound Engineer
                </p>
              </div>
              <img
                src="/img/timmy.jpg"
                alt="Timmy Phillips"
                class="w-60 h-40 md:w-50 rounded-full object-cover shadow-md"
                @click="startGame"
              />
            </div>

          <div class="border-t border-slate-200"></div>


            <p class="text-slate-700 leading-relaxed">
              Passionate audio professional with 5+ years of experience in
              recording, mixing, and mastering. Specializing in live sound.
            </p>

            <div>
              <div class="flex items-center gap-1 text-sm text-slate-700">
                <component :is="ChevronRight" class="w-4 h-4" /><p class="text-slate-700">Live sound engineer / <a href="https://www.instagram.com/vookoo_bristol/?hl=en-gb" class="text-right text-blue-500 cursor-pointer">@vookoo_bristol</a></p>
              </div>
              <div class="flex items-center gap-1 text-sm text-slate-700">
                <component :is="ChevronRight" class="w-4 h-4" /><p class="text-slate-700">Guitar & Sax / <a href="https://www.instagram.com/miyathesun/?hl=en-gb" class="text-blue-500 cursor-pointer">@miyathesun</a></p>
              </div>
            </div>
          </div>

          <div class="border-t border-slate-200"></div>

          <!-- Services -->
          <div class="space-y-6 flex flex-col gap-5">
            <h2
              class="text-xl font-semibold text-slate-900 flex items-center gap-2"
            >
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
              >
                {{ specialty }}
              </span>
            </div>
          </div>
        </div>

        <!-- Right Side - Contact & Social -->
        <div class="bg-slate-50 pt-5 md:pt-17 p-8 md:p-12 space-y-6 flex flex-col gap-2">
          <div class="space-y-4 flex flex-col gap-3">
            <h2 class="text-xl font-semibold text-slate-900">Get In Touch</h2>

            <div class="space-y-4">
              <div class="flex items-center gap-3 text-slate-700">
                <div class="p-2 bg-white rounded-lg shadow-sm">
                  <Mail class="w-4 h-4" />
                </div>
                <div>
                  <p class="text-sm text-slate-500">Email</p>
                  <p
                    class="underline text-blue-500 cursor-pointer"
                    @click="startProject"
                  >
                    timphillips243@gmail.com
                  </p>
                </div>
              </div>

              <div class="flex items-center gap-3 text-slate-700">
                <div class="p-2 bg-white rounded-lg shadow-sm">
                  <Phone class="w-4 h-4" />
                </div>
                <div>
                  <p class="text-sm text-slate-500">Phone</p>
                  <a
                    :href="`tel:+${phoneNumberSafe}`"
                    class="underline text-blue-500"
                    >{{ phoneNumber }}</a
                  >
                </div>
              </div>

              <div
                @click="checkBeans()"
                class="flex items-center gap-3 text-slate-700"
              >
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
          <div class="space-y-6 flex flex-col gap-2">
            <h3 class="font-medium text-slate-900">Connect With Me</h3>
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
              <li v-for="equipment in studioEquipment" :key="equipment">
                • {{ equipment }}
              </li>
            </ul>
          </div>

          <!-- CTA -->
          <div class="pt-4">
            <button
              @click="startProject"
              class="w-full inline-flex gap-2 items-center justify-center rounded-md bg-slate-900 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-slate-800 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-slate-600"
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
import { ref } from "vue";
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
  MessageCircleHeart,
  ChevronRight
} from "lucide-vue-next";
import HeinzBeansRain from "./HeinzBeansRain.vue";

const specialties = ref(["Live sound", "Festivals", "Brass", "Guitar", "Saxophone"]);
const phoneNumber = ref("+44 7463 818156");
const phoneNumberSafe = phoneNumber.value.replace(/\D/g, ""); // Remove non-numeric characters

const getWhatsAppLink = () => {
  const message = "Hello, I would like to enquire about a project!"; // Replace with your message
  const encodedMessage = encodeURI(message);
  const phoneNumberSafe = phoneNumber.value.replace(/\D/g, ""); // Remove non-numeric characters
  return `https://wa.me/${phoneNumberSafe}?text=${encodedMessage}`;
};

const socialLinks = ref([
  {
    name: "Instagram",
    icon: Instagram,
    url: "https://www.instagram.com/_timmyphilly_?igsh=MXc2bWpzMGtsODYwZg==",
  },
  { name: "WhatsApp", icon: MessageCircleHeart, url: getWhatsAppLink() },
]);

const studioEquipment = ref([
  "Pro Tools HDX System",
  "Neumann & AKG Microphones",
  "SSL Console",
  "Genelec Monitoring",
]);

const openLink = (url) => {
  window.open(url, "_blank");
};

const startProject = () => {
  window.location.href =
    "mailto:timphillips243@gmail.com?subject=New Project Inquiry";
};

const beanCount = ref(0);
const gameStarted = ref(false);
const checkBeans = () => {
  beanCount.value++;
};

const startGame = () => {
  if (beanCount.value < 10) return;
  gameStarted.value = true;
};
</script>

<style scoped>
/* Additional custom styles if needed */
.backdrop-blur-sm {
  backdrop-filter: blur(4px);
}
</style>
