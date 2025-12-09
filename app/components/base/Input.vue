<script setup lang="ts">
defineProps<{
  label: string
  modelValue: string
  type?: string
  placeholder?: string
  helpText?: string
}>()

const emit = defineEmits(['update:modelValue'])

const handleInput = (e: Event) => {
  const target = e.target as HTMLInputElement
  emit('update:modelValue', target.value)
}
</script>

<template>
  <div class="group">
    <label class="block text-xs font-medium text-slate-400 mb-1 ml-1 uppercase">{{ label }}</label>
    <div class="relative transition-all duration-300 transform focus-within:scale-[1.02]">
      <input 
        :type="type || 'text'"
        :value="modelValue"
        @input="handleInput"
        :placeholder="placeholder"
        class="w-full bg-slate-800/50 border border-slate-700 text-white rounded-xl px-4 py-3 focus:ring-2 focus:ring-indigo-500 outline-none placeholder-slate-500 transition-all" 
      />
    </div>
    <p v-if="helpText" class="text-[10px] text-slate-500 mt-1 ml-1">{{ helpText }}</p>
  </div>
</template>