<script setup lang="ts">
defineProps<{
  label: string
  modelValue: string
  options: { value: string; label: string }[]
}>()

const emit = defineEmits(['update:modelValue'])

const handleChange = (event: Event) => {
  const target = event.target as HTMLSelectElement
  emit('update:modelValue', target.value)
}
</script>

<template>
  <div class="flex flex-col gap-1.5">
    <label class="text-xs font-bold text-slate-400 uppercase tracking-wide ml-1">
      {{ label }}
    </label>
    <div class="relative">
      <select
        :value="modelValue"
        @change="handleChange"
        class="w-full bg-slate-800 border border-slate-700 text-white text-sm rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 block p-3 pr-8 appearance-none outline-none cursor-pointer transition-all"
      >
        <option v-for="opt in options" :key="opt.value" :value="opt.value">
          {{ opt.label }}
        </option>
      </select>
      <div class="absolute inset-y-0 right-0 flex items-center px-3 pointer-events-none text-slate-400">
        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
      </div>
    </div>
  </div>
</template>