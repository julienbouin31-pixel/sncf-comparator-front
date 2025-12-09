<script setup lang="ts">
defineProps<{
  label: string
  modelValue: string
  options: { value: string; label: string }[]
}>()

const emit = defineEmits(['update:modelValue'])

const handleChange = (e: Event) => {
  const target = e.target as HTMLSelectElement
  emit('update:modelValue', target.value)
}
</script>

<template>
  <div class="group">
    <label class="block text-xs font-medium text-slate-400 mb-1 ml-1 uppercase">{{ label }}</label>
    <div class="relative transition-all duration-300 transform focus-within:scale-[1.02]">
      <select 
        :value="modelValue"
        @change="handleChange"
        class="w-full appearance-none bg-slate-800/50 border border-slate-700 text-white rounded-xl px-4 py-3 focus:ring-2 focus:ring-indigo-500 outline-none cursor-pointer"
      >
        <option v-for="opt in options" :key="opt.value" :value="opt.value">
          {{ opt.label }}
        </option>
      </select>
      <div class="absolute right-4 top-4 pointer-events-none text-slate-400">
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m6 9 6 6 6-6"/></svg>
      </div>
    </div>
  </div>
</template>