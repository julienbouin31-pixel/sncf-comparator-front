<script setup lang="ts">
import { ref } from 'vue'

// Interface correspondant exactement à ton retour API
interface Station {
  id_station: number
  uic_code: string
  name: string
  latitude: string
  longitude: string
  commune?: {
    name: string
    postal_code: string
    department?: {
      name: string
      code: string
    }
  }
}

const props = defineProps<{
  label: string
  modelValue: string
  placeholder?: string
  colorFocus?: string 
}>()

const emit = defineEmits(['update:modelValue', 'select'])

const isOpen = ref(false)
const isLoading = ref(false)
const suggestions = ref<Station[]>([])
let debounceTimer: ReturnType<typeof setTimeout>

const handleInput = (e: Event) => {
  const val = (e.target as HTMLInputElement).value
  emit('update:modelValue', val)
  
  clearTimeout(debounceTimer)

  if (val.length < 2) {
    suggestions.value = []
    isOpen.value = false
    return
  }

  isLoading.value = true
  debounceTimer = setTimeout(async () => {
    try {
      // Utilisation de $fetch qui est natif à Nuxt
      const response = await $fetch<Station[]>('http://localhost:3001/stations/search', {
        params: { q: val }
      })
      
      // On copie les données pour éviter les soucis de proxy
      suggestions.value = [...response]
      isOpen.value = true
      
      // Debug : pour voir si les données arrivent bien
      console.log('Stations reçues:', suggestions.value)
    } catch (error) {
      console.error("Erreur API:", error)
      suggestions.value = []
    } finally {
      isLoading.value = false
    }
  }, 300)
}

const selectStation = (station: Station) => {
  emit('update:modelValue', station.name)
  emit('select', station)
  isOpen.value = false
}

const handleBlur = () => {
  // Petit délai pour laisser le temps au "click" sur la liste de se faire
  setTimeout(() => { isOpen.value = false }, 200)
}
</script>

<template>
  <div class="group relative z-20">
    <label class="block text-xs font-medium text-slate-400 mb-1 ml-1 uppercase">{{ label }}</label>
    
    <div class="relative transition-all duration-300 transform focus-within:scale-[1.02]">
      <input 
        type="text" 
        :value="modelValue"
        @input="handleInput"
        @blur="handleBlur"
        :placeholder="placeholder"
        autocomplete="off"
        class="w-full bg-slate-800/50 border border-slate-700 text-white rounded-xl px-4 py-4 outline-none placeholder-slate-500 transition-all focus:ring-2"
        :class="colorFocus === 'pink' ? 'focus:ring-pink-500' : 'focus:ring-indigo-500'"
      />
      
      <div class="absolute right-4 top-4 text-slate-500 flex items-center">
        <span v-if="isLoading" class="animate-spin mr-2">⟳</span>
        <slot name="icon"></slot>
      </div>

      <ul 
        v-if="isOpen && suggestions.length > 0" 
        class="absolute left-0 right-0 mt-2 bg-slate-800 border border-slate-700 rounded-xl shadow-xl z-50 max-h-60 overflow-y-auto custom-scrollbar"
      >
        <li 
          v-for="station in suggestions" 
          :key="station.id_station" 
          @mousedown.prevent="selectStation(station)"
          class="px-4 py-3 hover:bg-slate-700 cursor-pointer border-b border-slate-700/50 last:border-0 transition-colors group/item"
        >
          <div class="flex justify-between items-center">
            <span class="text-sm text-white font-medium">{{ station.name }}</span>
            <span class="text-xs text-slate-400 group-hover/item:text-slate-300">
              {{ station.commune?.postal_code || '' }}
            </span>
          </div>
          
          <div class="text-xs text-slate-500 mt-0.5">
            <span v-if="station.commune">
              {{ station.commune.name }} 
              <span v-if="station.commune.department">
                - {{ station.commune.department.name }} ({{ station.commune.department.code }})
              </span>
            </span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
/* Scrollbar personnalisée */
.custom-scrollbar::-webkit-scrollbar {
  width: 6px;
}
.custom-scrollbar::-webkit-scrollbar-track {
  background: #1e293b;
  border-radius: 8px;
}
.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #475569;
  border-radius: 8px;
}
</style>