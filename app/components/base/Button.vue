<script setup lang="ts">
defineProps<{
  loading?: boolean
}>()
</script>

<template>
  <button
    :disabled="loading"
    class="w-full text-white bg-indigo-600 hover:bg-indigo-700 focus:ring-4 focus:ring-indigo-900 font-medium rounded-lg text-sm px-5 py-3.5 text-center transition-all disabled:opacity-50 disabled:cursor-not-allowed shadow-lg shadow-indigo-500/20"
  >
    <span v-if="loading">Chargement...</span>
    <span v-else><slot /></span>
  </button>
</template>