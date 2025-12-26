<script setup lang="ts">
import { ref } from 'vue'

const loading = ref(false)
const simulationResult = ref<any>(null)
  
const handleSimulation = async (payload: any) => {
  loading.value = true
  simulationResult.value = null

  try {
    const data = await $fetch('http://localhost:3001/subscriptions/simulate', {
      method: 'POST',
      body: payload
    })
    simulationResult.value = data
    
    // Scroll auto vers les résultats
    setTimeout(() => {
      document.getElementById('results-section')?.scrollIntoView({ behavior: 'smooth', block: 'start' })
    }, 200)

  } catch (error) {
    console.error("Erreur simulation:", error)
    alert("Erreur lors du calcul.")
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="min-h-screen bg-slate-950 text-slate-200 font-sans selection:bg-indigo-500/30 selection:text-indigo-200 overflow-x-hidden relative flex flex-col">
    
    <div class="fixed inset-0 overflow-hidden pointer-events-none">
        <div class="absolute top-[-20%] left-1/2 -translate-x-1/2 w-[1000px] h-[600px] bg-indigo-900/10 rounded-full blur-[120px]"></div>
    </div>

    <div class="relative z-10 container mx-auto px-6 flex-grow flex flex-col justify-center items-center py-10 lg:py-16">
      
      <TheHero />
      
      <SimulationForm 
        :loading="loading" 
        @submit="handleSimulation" 
      />

      <SimulationResults 
        v-if="simulationResult" 
        :result="simulationResult" 
      />

      <TheFooter />
      
    </div>
  </div>
</template>