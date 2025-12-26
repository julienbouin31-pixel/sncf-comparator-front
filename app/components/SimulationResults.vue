<script setup lang="ts">
interface Recommendation {
  combo_name: string // Nouveau champ API
  provider: string
  monthly_subscription_cost: string // Nouveau nom
  total_monthly_bill: string // Nouveau nom
  estimated_savings: string
  is_profitable: boolean
  details: string
  is_combo: boolean // Nouveau champ
  coverage_breakdown: string[] // Liste des détails de couverture
  reduction_info?: string // (Peut ne plus être présent ou être dans breakdown)
}

interface SimulationContext {
  age: number
  status: string
  total_trips_count: number
  trips_summary: string[]
}

interface SimulationResult {
  context: SimulationContext
  estimated_cost_without_subscription: string
  recommendations: Recommendation[]
}

defineProps<{
  result: SimulationResult
}>()
</script>

<template>
  <div id="results-section" class="w-full max-w-6xl mt-12 pb-16 animate-slide-up">
    
    <div class="flex items-center gap-4 mb-8">
      <h2 class="text-2xl font-bold text-white tracking-tight">Analyse Global</h2>
      <div class="h-px flex-grow bg-slate-800"></div>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-8">
      <div class="bg-slate-900 border border-slate-800 p-5 rounded-2xl md:col-span-2">
        <p class="text-[10px] font-bold text-slate-500 uppercase tracking-wider mb-2">Trajets Analysés ({{ result.context.total_trips_count }})</p>
        <ul class="space-y-1">
          <li v-for="(summary, idx) in result.context.trips_summary" :key="idx" class="text-sm text-slate-300 flex items-center gap-2">
            <span class="w-1.5 h-1.5 rounded-full bg-indigo-500"></span>
            {{ summary }}
          </li>
        </ul>
      </div>
      
      <div class="bg-red-500/5 border border-red-500/10 p-5 rounded-2xl flex flex-col justify-center">
        <p class="text-[10px] font-bold text-red-400 uppercase tracking-wider mb-1">Coût Total Sans Carte</p>
        <p class="font-black text-3xl text-red-500">{{ result.estimated_cost_without_subscription }}</p>
        <p class="text-xs text-red-400/60 mt-1">Mensuel estimé</p>
      </div>
    </div>

    <div class="space-y-4">
      <div 
        v-for="(rec, index) in result.recommendations" 
        :key="index"
        class="group relative overflow-hidden rounded-2xl border transition-all duration-300"
        :class="rec.is_profitable 
          ? 'bg-slate-900 border-emerald-900/50 shadow-lg shadow-emerald-900/10 hover:border-emerald-500/30' 
          : 'bg-slate-900/50 border-slate-800 opacity-60'"
      >
        <div v-if="rec.is_profitable" class="absolute left-0 top-0 bottom-0 w-1 bg-emerald-500"></div>

        <div class="p-6 grid grid-cols-1 md:grid-cols-12 gap-6 relative z-10">
          
          <div class="md:col-span-8">
            <div class="flex items-start gap-3 mb-2">
              <span v-if="rec.is_profitable" class="mt-1 bg-emerald-500/10 text-emerald-400 text-[10px] font-bold px-2 py-0.5 rounded border border-emerald-500/20 uppercase tracking-wider whitespace-nowrap">
                {{ index === 0 ? 'Meilleure Option' : 'Rentable' }}
              </span>
              <span v-if="rec.is_combo" class="mt-1 bg-indigo-500/10 text-indigo-400 text-[10px] font-bold px-2 py-0.5 rounded border border-indigo-500/20 uppercase tracking-wider whitespace-nowrap">
                Combo
              </span>
              <h3 class="text-lg md:text-xl font-bold text-slate-200 leading-tight">{{ rec.combo_name }}</h3>
            </div>
            
            <p class="text-sm text-slate-500 mb-4">{{ rec.provider }}</p>

            <div class="bg-black/20 rounded-xl p-3 border border-white/5 space-y-2">
              <p class="text-[10px] font-bold text-slate-500 uppercase">Détail de la couverture</p>
              <div v-for="(detail, i) in rec.coverage_breakdown" :key="i" class="text-xs text-slate-300 flex gap-2">
                 <span>{{ detail }}</span>
              </div>
            </div>
          </div>

          <div class="md:col-span-4 flex flex-col justify-center items-end border-t md:border-t-0 md:border-l border-slate-800 pt-4 md:pt-0 md:pl-6">
            <div class="text-right mb-3">
              <p class="text-[10px] font-bold text-slate-500 uppercase tracking-wider mb-0.5">Nouvelle Facture</p>
              <p class="text-2xl font-bold text-white">{{ rec.total_monthly_bill }}</p>
              <p class="text-[10px] text-slate-400">incluant abo. {{ rec.monthly_subscription_cost }}</p>
            </div>

            <div class="text-right">
              <p class="text-[10px] font-bold text-emerald-500 uppercase tracking-wider mb-0.5">Économie / mois</p>
              <p class="text-3xl font-black tracking-tighter" :class="rec.is_profitable ? 'text-emerald-400' : 'text-slate-500'">
                {{ rec.is_profitable ? '+' + rec.estimated_savings : '0€' }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<style scoped>
.animate-slide-up { animation: slideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards; opacity: 0; transform: translateY(40px); }
@keyframes slideUp { to { opacity: 1; transform: translateY(0); } }
</style>