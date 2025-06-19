<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-900 via-slate-800 to-slate-900 flex items-center justify-center p-4">
    <div class="w-full max-w-2xl bg-white/95 backdrop-blur-sm shadow-2xl rounded-lg overflow-hidden">
      <div class="p-8">
        <!-- Header -->
        <div class="text-center mb-8">
          <div class="flex items-center justify-center gap-3 mb-4">
            <div class="p-3 bg-slate-900 rounded-lg">
              <AudioWaveformIcon class="w-8 h-8 text-white" />
            </div>
            <div>
              <h1 class="text-2xl font-bold text-slate-900">Start Your Project</h1>
              <p class="text-slate-600">Let's create something amazing together</p>
            </div>
          </div>
        </div>

        <!-- Success Message -->
        <div v-if="showSuccess" class="mb-6 p-4 bg-green-50 border border-green-200 rounded-lg">
          <div class="flex items-center gap-2 text-green-800">
            <CheckCircle class="w-5 h-5" />
            <p class="font-medium">Message sent successfully!</p>
          </div>
          <p class="text-green-700 text-sm mt-1">I'll get back to you within 24 hours.</p>
        </div>

        <!-- Contact Form -->
        <form @submit.prevent="submitForm" class="space-y-6">
          <!-- Name Field -->
          <div>
            <label for="name" class="block text-sm font-medium text-slate-700 mb-2">
              Full Name *
            </label>
            <input
              id="name"
              v-model="form.name"
              type="text"
              :class="[
                'w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-slate-500 focus:border-slate-500 transition-colors',
                errors.name ? 'border-red-300 bg-red-50' : 'border-slate-300'
              ]"
              placeholder="Enter your full name"
              @blur="validateField('name')"
            />
            <p v-if="errors.name" class="mt-1 text-sm text-red-600 flex items-center gap-1">
              <AlertCircle class="w-4 h-4" />
              {{ errors.name }}
            </p>
          </div>

          <!-- Email Field -->
          <div>
            <label for="email" class="block text-sm font-medium text-slate-700 mb-2">
              Email Address *
            </label>
            <input
              id="email"
              v-model="form.email"
              type="email"
              :class="[
                'w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-slate-500 focus:border-slate-500 transition-colors',
                errors.email ? 'border-red-300 bg-red-50' : 'border-slate-300'
              ]"
              placeholder="your.email@example.com"
              @blur="validateField('email')"
            />
            <p v-if="errors.email" class="mt-1 text-sm text-red-600 flex items-center gap-1">
              <AlertCircle class="w-4 h-4" />
              {{ errors.email }}
            </p>
          </div>

          <!-- Phone Field -->
          <div>
            <label for="phone" class="block text-sm font-medium text-slate-700 mb-2">
              Phone Number
            </label>
            <input
              id="phone"
              v-model="form.phone"
              type="tel"
              :class="[
                'w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-slate-500 focus:border-slate-500 transition-colors',
                errors.phone ? 'border-red-300 bg-red-50' : 'border-slate-300'
              ]"
              placeholder="+1 (555) 123-4567"
              @blur="validateField('phone')"
            />
            <p v-if="errors.phone" class="mt-1 text-sm text-red-600 flex items-center gap-1">
              <AlertCircle class="w-4 h-4" />
              {{ errors.phone }}
            </p>
          </div>

          <!-- Project Type -->
          <div>
            <label for="projectType" class="block text-sm font-medium text-slate-700 mb-2">
              Project Type *
            </label>
            <select
              id="projectType"
              v-model="form.projectType"
              :class="[
                'w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-slate-500 focus:border-slate-500 transition-colors',
                errors.projectType ? 'border-red-300 bg-red-50' : 'border-slate-300'
              ]"
              @change="validateField('projectType')"
            >
              <option value="">Select a project type</option>
              <option v-for="type in projectTypes" :key="type" :value="type">
                {{ type }}
              </option>
            </select>
            <p v-if="errors.projectType" class="mt-1 text-sm text-red-600 flex items-center gap-1">
              <AlertCircle class="w-4 h-4" />
              {{ errors.projectType }}
            </p>
          </div>

          <!-- Budget Range -->
          <div>
            <label for="budget" class="block text-sm font-medium text-slate-700 mb-2">
              Budget Range
            </label>
            <select
              id="budget"
              v-model="form.budget"
              class="w-full px-4 py-3 border border-slate-300 rounded-lg focus:ring-2 focus:ring-slate-500 focus:border-slate-500 transition-colors"
            >
              <option value="">Select budget range</option>
              <option v-for="range in budgetRanges" :key="range" :value="range">
                {{ range }}
              </option>
            </select>
          </div>

          <!-- Message Field -->
          <div>
            <label for="message" class="block text-sm font-medium text-slate-700 mb-2">
              Project Details *
            </label>
            <textarea
              id="message"
              v-model="form.message"
              rows="5"
              :class="[
                'w-full px-4 py-3 border rounded-lg focus:ring-2 focus:ring-slate-500 focus:border-slate-500 transition-colors resize-none',
                errors.message ? 'border-red-300 bg-red-50' : 'border-slate-300'
              ]"
              placeholder="Tell me about your project, timeline, and any specific requirements..."
              @blur="validateField('message')"
            ></textarea>
            <p v-if="errors.message" class="mt-1 text-sm text-red-600 flex items-center gap-1">
              <AlertCircle class="w-4 h-4" />
              {{ errors.message }}
            </p>
            <p class="mt-1 text-sm text-slate-500">
              {{ form.message.length }}/500 characters
            </p>
          </div>

          <!-- File Upload -->
          <div>
            <label for="files" class="block text-sm font-medium text-slate-700 mb-2">
              Audio References (Optional)
            </label>
            <div class="border-2 border-dashed border-slate-300 rounded-lg p-6 text-center hover:border-slate-400 transition-colors">
              <input
                id="files"
                ref="fileInput"
                type="file"
                multiple
                accept="audio/*,.mp3,.wav,.aiff,.flac"
                class="hidden"
                @change="handleFileUpload"
              />
              <button
                type="button"
                @click="$refs.fileInput.click()"
                class="inline-flex items-center gap-2 text-slate-600 hover:text-slate-800"
              >
                <Upload class="w-5 h-5" />
                Click to upload audio files
              </button>
              <p class="text-sm text-slate-500 mt-2">MP3, WAV, AIFF, FLAC up to 10MB each</p>
              
              <!-- Selected Files -->
              <div v-if="selectedFiles.length > 0" class="mt-4 space-y-2">
                <div
                  v-for="(file, index) in selectedFiles"
                  :key="index"
                  class="flex items-center justify-between bg-slate-50 p-2 rounded text-sm"
                >
                  <span class="flex items-center gap-2">
                    <Music class="w-4 h-4 text-slate-500" />
                    {{ file.name }}
                  </span>
                  <button
                    type="button"
                    @click="removeFile(index)"
                    class="text-red-500 hover:text-red-700"
                  >
                    <X class="w-4 h-4" />
                  </button>
                </div>
              </div>
            </div>
          </div>

          <!-- Submit Button -->
          <div class="pt-4">
            <button
              type="submit"
              :disabled="isSubmitting || !isFormValid"
              :class="[
                'w-full inline-flex items-center justify-center px-6 py-3 text-sm font-semibold rounded-lg transition-all duration-200',
                isFormValid && !isSubmitting
                  ? 'bg-slate-900 text-white hover:bg-slate-800 focus:ring-2 focus:ring-slate-500'
                  : 'bg-slate-300 text-slate-500 cursor-not-allowed'
              ]"
            >
              <template v-if="isSubmitting">
                <div class="animate-spin rounded-full h-4 w-4 border-2 border-white border-t-transparent mr-2"></div>
                Sending Message...
              </template>
              <template v-else>
                <Send class="w-4 h-4 mr-2" />
                Send Message
              </template>
            </button>
          </div>
        </form>

        <!-- Contact Info Footer -->
        <div class="mt-8 pt-6 border-t border-slate-200 text-center">
          <p class="text-slate-600 text-sm">
            Prefer to call? Reach me at 
            <a href="tel:+15551234567" class="font-medium text-slate-900 hover:underline">
              +1 (555) 123-4567
            </a>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, reactive } from 'vue'
import {
  AudioWaveformIcon,
  CheckCircle,
  AlertCircle,
  Upload,
  Music,
  X,
  Send
} from 'lucide-vue-next'

// Form data
const form = reactive({
  name: '',
  email: '',
  phone: '',
  projectType: '',
  budget: '',
  message: ''
})

// Form validation errors
const errors = reactive({
  name: '',
  email: '',
  phone: '',
  projectType: '',
  message: ''
})

// Form state
const isSubmitting = ref(false)
const showSuccess = ref(false)
const selectedFiles = ref([])

// Options
const projectTypes = ref([
  'Music Recording & Production',
  'Mixing & Mastering',
  'Live Sound Engineering',
  'Podcast Production',
  'Film/Video Post-Production',
  'Voice Over Recording',
  'Audio Restoration',
  'Other'
])

const budgetRanges = ref([
  'Under $500',
  '$500 - $1,000',
  '$1,000 - $2,500',
  '$2,500 - $5,000',
  '$5,000 - $10,000',
  'Over $10,000',
  'Discuss Budget'
])

// Validation functions
const validateEmail = (email) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return emailRegex.test(email)
}

const validatePhone = (phone) => {
  if (!phone) return true // Phone is optional
  const phoneRegex = /^[\+]?[1-9][\d]{0,15}$/
  return phoneRegex.test(phone.replace(/[\s\-$$$$]/g, ''))
}

const validateField = (fieldName) => {
  errors[fieldName] = ''

  switch (fieldName) {
    case 'name':
      if (!form.name.trim()) {
        errors.name = 'Name is required'
      } else if (form.name.trim().length < 2) {
        errors.name = 'Name must be at least 2 characters'
      }
      break

    case 'email':
      if (!form.email.trim()) {
        errors.email = 'Email is required'
      } else if (!validateEmail(form.email)) {
        errors.email = 'Please enter a valid email address'
      }
      break

    case 'phone':
      if (form.phone && !validatePhone(form.phone)) {
        errors.phone = 'Please enter a valid phone number'
      }
      break

    case 'projectType':
      if (!form.projectType) {
        errors.projectType = 'Please select a project type'
      }
      break

    case 'message':
      if (!form.message.trim()) {
        errors.message = 'Project details are required'
      } else if (form.message.trim().length < 10) {
        errors.message = 'Please provide more details (at least 10 characters)'
      } else if (form.message.length > 500) {
        errors.message = 'Message is too long (maximum 500 characters)'
      }
      break
  }
}

// Computed property to check if form is valid
const isFormValid = computed(() => {
  // Check required fields
  const requiredFieldsFilled = form.name.trim() && 
                              form.email.trim() && 
                              form.projectType && 
                              form.message.trim()

  // Check if there are no errors
  const noErrors = !Object.values(errors).some(error => error !== '')

  return requiredFieldsFilled && noErrors
})

// File handling
const handleFileUpload = (event) => {
  const files = Array.from(event.target.files)
  const maxSize = 10 * 1024 * 1024 // 10MB
  
  files.forEach(file => {
    if (file.size > maxSize) {
      alert(`File ${file.name} is too large. Maximum size is 10MB.`)
      return
    }
    
    if (selectedFiles.value.length < 5) { // Limit to 5 files
      selectedFiles.value.push(file)
    } else {
      alert('Maximum 5 files allowed')
    }
  })
  
  // Clear the input
  event.target.value = ''
}

const removeFile = (index) => {
  selectedFiles.value.splice(index, 1)
}

// Form submission
const submitForm = async () => {
  // Validate all fields
  Object.keys(errors).forEach(field => validateField(field))
  
  if (!isFormValid.value) {
    return
  }

  isSubmitting.value = true

  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Here you would typically send the form data to your backend
    console.log('Form submitted:', {
      ...form,
      files: selectedFiles.value
    })

    // Show success message
    showSuccess.value = true
    
    // Reset form
    Object.keys(form).forEach(key => {
      form[key] = ''
    })
    selectedFiles.value = []
    
    // Hide success message after 5 seconds
    setTimeout(() => {
      showSuccess.value = false
    }, 5000)

  } catch (error) {
    console.error('Form submission error:', error)
    alert('There was an error sending your message. Please try again.')
  } finally {
    isSubmitting.value = false
  }
}
</script>

<style scoped>
.backdrop-blur-sm {
  backdrop-filter: blur(4px);
}

/* Custom scrollbar for textarea */
textarea::-webkit-scrollbar {
  width: 6px;
}

textarea::-webkit-scrollbar-track {
  background: #f1f5f9;
  border-radius: 3px;
}

textarea::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 3px;
}

textarea::-webkit-scrollbar-thumb:hover {
  background: #94a3b8;
}
</style>