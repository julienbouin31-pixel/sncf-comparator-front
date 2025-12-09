<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps<{
  label: string
  modelValue: string
}>()

const emit = defineEmits(['update:modelValue'])

const query = ref(props.modelValue)
const isOpen = ref(false)
const suggestions = ref<string[]>([])

// Simule un appel API (à remplacer par ton backend NestJS plus tard)
const handleInput = (e: Event) => {
  const val = (e.target as HTMLInputElement).value
  query.value = val
  emit('update:modelValue', val)

  if (val.length > 2) {
    isOpen.value = true
    // Fake results pour l'instant
    suggestions.value = [
      `${val} Ville`,
      `${val} Centre`,
      `${val} TGV`
    ]
  } else {
    isOpen.value = false
  }
}

const selectStation = (name: string) => {
  query.value = name
  emit('update:modelValue', name)
  isOpen.value = false
}

// Fermer si on clique ailleurs (simple blur pour l'instant)
const handleBlur = () => {
  // Petit délai pour permettre le clic sur la liste
  setTimeout(() => { isOpen.value = false }, 200)
}
</script>

<template>
  <div class="flex flex-col gap-1.5 relative">
    <label class="text-xs font-bold text-slate-400 uppercase tracking-wide ml-1">
      {{ label }}
    </label>
    <div class="relative">
      <input
        type="text"
        v-model="query"
        @input="handleInput"
        @blur="handleBlur"
        @focus="query.length > 2 ? isOpen = true : null"
        placeholder="Ex: Paris Montparnasse"
        class="w-full bg-slate-800 border border-slate-700 text-white text-sm rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 block p-3 outline-none transition-all placeholder-slate-500"
      />
      
      <ul 
        v-if="isOpen && suggestions.length"
        class="absolute z-50 w-full bg-slate-700 border border-slate-600 rounded-lg mt-1 shadow-xl max-h-48 overflow-y-auto"
      >
        <li
          v-for="(station, index) in suggestions" 
          :key="index"
          @click="selectStation(station)"
          class="px-4 py-2 hover:bg-slate-600 cursor-pointer text-slate-200 text-sm transition-colors"
        >
          📍 {{ station }}
        </li>
      </ul>
    </div>
  </div>
</template>