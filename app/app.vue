<script setup lang="ts">
import { ref, computed } from 'vue'

// --- State (Données du formulaire) ---
// On sépare le texte affiché (v-model) de l'objet gare sélectionné (pour l'ID)
const departureName = ref('')
const arrivalName = ref('')

// Stockage des objets gares complets (avec id_station, uic_code, etc.)
const selectedDeparture = ref<any>(null)
const selectedArrival = ref<any>(null)

const birthDate = ref('')
const status = ref('employee')
const frequency = ref(4)

// --- Listes & Options ---
const statuses = [
  { value: 'employee', label: '👔 Salarié' },
  { value: 'student', label: '🎓 Étudiant (-26 ans)' },
  { value: 'student_scholarship', label: '🎓 Étudiant Boursier' },
  { value: 'job_seeker', label: '💼 Demandeur d\'emploi' },
  { value: 'military', label: '🪖 Militaire' },
  { value: 'retired', label: '👴 Retraité' },
]

const frequencyLabel = computed(() => {
  return frequency.value === 1 
    ? '1 aller-retour / mois' 
    : `${frequency.value} allers-retours / mois`
})

// --- Handlers (Actions) ---

// Appelée quand l'utilisateur clique sur une gare dans la liste "Départ"
const onSelectDeparture = (station: any) => {
  selectedDeparture.value = station
  console.log("✅ Gare départ sélectionnée :", station.name, "(ID:", station.id_station, ")")
}

// Appelée quand l'utilisateur clique sur une gare dans la liste "Arrivée"
const onSelectArrival = (station: any) => {
  selectedArrival.value = station
  console.log("✅ Gare arrivée sélectionnée :", station.name, "(ID:", station.id_station, ")")
}

// Lancement de la simulation
const handleSearch = () => {
  // Construction du payload pour le backend
  const payload = {
    departure_station_id: selectedDeparture.value?.id_station,
    arrival_station_id: selectedArrival.value?.id_station,
    birth_date: birthDate.value,
    status: status.value,
    frequency: frequency.value
  }
  
  console.log("🚀 Envoi de la simulation :", payload)
  
  // Validation basique
  if (!payload.departure_station_id || !payload.arrival_station_id) {
    alert("Oups ! Veuillez sélectionner des gares valides (cliquez sur les suggestions).")
    return
  }
  
  alert(`Simulation lancée !\nDe : ${selectedDeparture.value.name}\nVers : ${selectedArrival.value.name}`)
  // TODO: Appel API réel ici (ex: await $fetch('/api/simulate', ...))
}
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
          Ne payez plus le prix fort. Simulez instantanément la rentabilité de toutes les cartes et abonnements SNCF/TER.
        </p>
      </div>

      <div class="w-full max-w-4xl bg-white/5 backdrop-blur-xl border border-white/10 rounded-3xl shadow-2xl p-8 md:p-10">
        
        <div class="grid grid-cols-1 md:grid-cols-12 gap-8">
          
          <div class="md:col-span-7 space-y-6">
            <h3 class="text-xl font-semibold text-white mb-4 flex items-center gap-2">
              <span class="bg-indigo-500 w-8 h-8 rounded-lg flex items-center justify-center text-sm">📍</span>
              Votre Itinéraire
            </h3>
            
            <div class="space-y-4 relative">
              
              <div class="relative z-50">
                <StationSearch 
                  label="Départ" 
                  v-model="departureName" 
                  @select="onSelectDeparture"
                  placeholder="Ex: Lille Flandres"
                  color-focus="indigo"
                >
                  <template #icon>
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="3"/></svg>
                  </template>
                </StationSearch>
              </div>

              <div class="hidden md:block absolute left-[1.6rem] top-[3.5rem] h-24 w-0.5 bg-gradient-to-b from-indigo-500 to-pink-500 opacity-30 z-0 pointer-events-none"></div>

              <div class="relative z-40">
                <StationSearch 
                  label="Arrivée" 
                  v-model="arrivalName" 
                  @select="onSelectArrival"
                  placeholder="Ex: Paris Nord"
                  color-focus="pink"
                >
                  <template #icon>
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M20 10c0 6-8 12-8 12s-8-6-8-12a8 8 0 0 1 16 0Z"/><circle cx="12" cy="10" r="3"/></svg>
                  </template>
                </StationSearch>
              </div>

            </div>
            
            <div class="pt-4 relative z-30">
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

                <div class="space-y-4">
                  <BaseInput 
                    label="Date de naissance" 
                    type="date" 
                    v-model="birthDate" 
                    help-text="Pour calculer l'âge (Cartes Jeune/Senior)"
                  />

                  <BaseSelect 
                    label="Statut" 
                    v-model="status" 
                    :options="statuses" 
                  />
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