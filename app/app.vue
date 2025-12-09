<script setup lang="ts">
import { ref, computed } from 'vue'

// --- État du formulaire ---
const departure = ref('')
const arrival = ref('')
const birthDate = ref('')
const status = ref('employee')
const frequency = ref(4)

// --- États UI (Simulation d'autocomplete) ---
const isFocusDep = ref(false)
const isFocusArr = ref(false)

// Liste des statuts basée sur ton enum backend
const statuses = [
  { value: 'employee', label: '👔 Salarié' },
  { value: 'student', label: '🎓 Étudiant (-26 ans)' },
  { value: 'student_scholarship', label: '🎓 Étudiant Boursier' },
  { value: 'job_seeker', label: '💼 Demandeur d\'emploi' },
  { value: 'military', label: '🪖 Militaire' },
  { value: 'retired', label: '👴 Retraité' },
]

// --- Logique (Placeholder pour l'appel API) ---
const handleSearch = () => {
  console.log({
    dep: departure.value,
    arr: arrival.value,
    date: birthDate.value,
    status: status.value,
    freq: frequency.value
  })
  alert("Lancement de la simulation... (Connecter le backend ici)")
}

// Formatage du texte de fréquence
const frequencyLabel = computed(() => {
  if (frequency.value === 1) return '1 aller-retour / mois'
  return `${frequency.value} allers-retours / mois`
})
</script>

<template>
  <div class="min-h-screen bg-slate-900 text-slate-100 font-sans selection:bg-indigo-500 selection:text-white overflow-hidden relative">
    
    <div class="absolute top-0 left-0 w-full h-full overflow-hidden z-0 pointer-events-none">
      <div class="absolute top-[-10%] left-[-10%] w-96 h-96 bg-indigo-600 rounded-full mix-blend-multiply filter blur-3xl opacity-40 animate-blob"></div>
      <div class="absolute top-[-10%] right-[-10%] w-96 h-96 bg-purple-600 rounded-full mix-blend-multiply filter blur-3xl opacity-40 animate-blob animation-delay-2000"></div>
      <div class="absolute bottom-[-20%] left-[20%] w-96 h-96 bg-pink-600 rounded-full mix-blend-multiply filter blur-3xl opacity-40 animate-blob animation-delay-4000"></div>
    </div>

    <div class="relative z-10 container mx-auto px-4 h-screen flex flex-col justify-center items-center">
      
      <div class="text-center mb-10 max-w-2xl">
        <div class="inline-block mb-4 px-3 py-1 rounded-full bg-indigo-500/10 border border-indigo-500/20 text-indigo-300 text-xs font-semibold uppercase tracking-wider">
          V 1.0 Alpha
        </div>
        <h1 class="text-5xl md:text-6xl font-extrabold mb-6 bg-clip-text text-transparent bg-gradient-to-r from-white via-indigo-200 to-indigo-400">
          Optimisez vos trajets.
        </h1>
        <p class="text-lg text-slate-400">
          Ne payez plus le prix fort. Simulez instantanément la rentabilité de toutes les cartes et abonnements SNCF/TER selon vos habitudes réelles.
        </p>
      </div>

      <div class="w-full max-w-4xl bg-white/5 backdrop-blur-xl border border-white/10 rounded-3xl shadow-2xl p-8 md:p-10">
        
        <div class="grid grid-cols-1 md:grid-cols-12 gap-6">
          
          <div class="md:col-span-7 space-y-6">
            <h3 class="text-xl font-semibold text-white mb-4 flex items-center gap-2">
              <span class="bg-indigo-500 w-8 h-8 rounded-lg flex items-center justify-center text-sm">📍</span>
              Votre Itinéraire
            </h3>
            
            <div class="space-y-4 relative">
              <div class="group">
                <label class="block text-xs font-medium text-slate-400 mb-1 ml-1 uppercase">Départ</label>
                <div class="relative transition-all duration-300 transform focus-within:scale-[1.02]">
                  <input 
                    type="text" 
                    v-model="departure"
                    @focus="isFocusDep = true" @blur="isFocusDep = false"
                    placeholder="Ex: Lille Flandres"
                    class="w-full bg-slate-800/50 border border-slate-700 text-white rounded-xl px-4 py-4 focus:ring-2 focus:ring-indigo-500 focus:border-transparent outline-none placeholder-slate-500 transition-all"
                  />
                  <div class="absolute right-4 top-4 text-slate-500">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="3"/></svg>
                  </div>
                </div>
              </div>

              <div class="hidden md:block absolute left-[1.6rem] top-[4.5rem] h-8 w-0.5 bg-gradient-to-b from-indigo-500 to-transparent opacity-30"></div>

              <div class="group">
                <label class="block text-xs font-medium text-slate-400 mb-1 ml-1 uppercase">Arrivée</label>
                <div class="relative transition-all duration-300 transform focus-within:scale-[1.02]">
                  <input 
                    type="text" 
                    v-model="arrival"
                    placeholder="Ex: Paris Nord"
                    class="w-full bg-slate-800/50 border border-slate-700 text-white rounded-xl px-4 py-4 focus:ring-2 focus:ring-pink-500 focus:border-transparent outline-none placeholder-slate-500 transition-all"
                  />
                  <div class="absolute right-4 top-4 text-slate-500">
                     <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M20 10c0 6-8 12-8 12s-8-6-8-12a8 8 0 0 1 16 0Z"/><circle cx="12" cy="10" r="3"/></svg>
                  </div>
                </div>
              </div>
            </div>
            
            <div class="pt-4">
               <div class="flex justify-between items-end mb-2">
                 <label class="block text-xs font-medium text-slate-400 uppercase">Fréquence mensuelle</label>
                 <span class="text-indigo-300 font-bold text-lg">{{ frequencyLabel }}</span>
               </div>
               <input 
                 type="range" v-model="frequency" min="1" max="30" step="1"
                 class="w-full h-2 bg-slate-700 rounded-lg appearance-none cursor-pointer accent-indigo-500 hover:accent-indigo-400 transition-all"
               />
               <div class="flex justify-between text-xs text-slate-500 mt-1 px-1">
                 <span>Occasionnel</span>
                 <span>Quotidien</span>
               </div>
            </div>
          </div>

          <div class="hidden md:block md:col-span-1 border-r border-slate-700/50 mx-auto h-full"></div>

          <div class="md:col-span-4 flex flex-col justify-between space-y-6">
             <div>
                <h3 class="text-xl font-semibold text-white mb-4 flex items-center gap-2">
                  <span class="bg-pink-500 w-8 h-8 rounded-lg flex items-center justify-center text-sm">👤</span>
                  Votre Profil
                </h3>

                <div class="mb-4">
                  <label class="block text-xs font-medium text-slate-400 mb-1 ml-1 uppercase">Date de naissance</label>
                  <input 
                    type="date" 
                    v-model="birthDate"
                    class="w-full bg-slate-800/50 border border-slate-700 text-white rounded-xl px-4 py-3 focus:ring-2 focus:ring-indigo-500 outline-none" 
                  />
                  <p class="text-[10px] text-slate-500 mt-1 ml-1">Nécessaire pour les cartes Jeune / Senior</p>
                </div>

                <div>
                  <label class="block text-xs font-medium text-slate-400 mb-1 ml-1 uppercase">Statut</label>
                  <div class="relative">
                    <select v-model="status" class="w-full appearance-none bg-slate-800/50 border border-slate-700 text-white rounded-xl px-4 py-3 focus:ring-2 focus:ring-indigo-500 outline-none cursor-pointer">
                      <option v-for="s in statuses" :key="s.value" :value="s.value">{{ s.label }}</option>
                    </select>
                    <div class="absolute right-4 top-4 pointer-events-none text-slate-400">
                      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m6 9 6 6 6-6"/></svg>
                    </div>
                  </div>
                </div>
             </div>

             <button 
                @click="handleSearch"
                class="group w-full relative overflow-hidden bg-gradient-to-r from-indigo-600 to-purple-600 hover:from-indigo-500 hover:to-purple-500 text-white font-bold py-4 rounded-xl shadow-lg shadow-indigo-500/30 transform transition-all duration-200 hover:scale-[1.02] active:scale-[0.98]"
             >
                <span class="relative z-10 flex items-center justify-center gap-2">
                  Calculer mes économies
                  <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="transition-transform group-hover:translate-x-1"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
                </span>
             </button>
          </div>
        </div>

      </div>
      
      <div class="mt-8 text-slate-500 text-sm flex gap-6">
         <span class="hover:text-slate-300 cursor-pointer transition-colors">Comment ça marche ?</span>
         <span class="hover:text-slate-300 cursor-pointer transition-colors">Confidentialité</span>
      </div>

    </div>
  </div>
</template>

<style>
/* Animation custom pour les blobs d'arrière-plan */
@keyframes blob {
  0% { transform: translate(0px, 0px) scale(1); }
  33% { transform: translate(30px, -50px) scale(1.1); }
  66% { transform: translate(-20px, 20px) scale(0.9); }
  100% { transform: translate(0px, 0px) scale(1); }
}
.animate-blob {
  animation: blob 7s infinite;
}
.animation-delay-2000 {
  animation-delay: 2s;
}
.animation-delay-4000 {
  animation-delay: 4s;
}
</style>