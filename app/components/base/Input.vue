<script setup lang="ts">
defineProps<{
  label: string
  modelValue: string | number
  type?: string
  placeholder?: string
}>()

// On définit l'emit
const emit = defineEmits(['update:modelValue'])

// Fonction propre pour gérer l'input et le typage
const handleInput = (event: Event) => {
  const target = event.target as HTMLInputElement
  emit('update:modelValue', target.value)
}
</script>

<template>
  <div class="flex flex-col gap-1.5">
    <label class="text-xs font-bold text-slate-400 uppercase tracking-wide ml-1">
      {{ label }}
    </label>
    <input
      :type="type || 'text'"
      :value="modelValue"
      @input="handleInput"
      :placeholder="placeholder"
      class="w-full bg-slate-800 border border-slate-700 text-white text-sm rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 block p-3 outline-none transition-all placeholder-slate-500"
    />
  </div>
</template>