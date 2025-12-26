<script setup lang="ts">
import { ref, computed } from 'vue'

// --- Interfaces ---
interface ConnectionStatus {
  loading: boolean
  available: boolean | null
  message: string | null
  // MODIFICATION : On attend un tableau de chaînes maintenant
  transport_types: string[] 
}

interface TripInput {
  id: number // Pour la clé v-for
  departureName: string
  arrivalName: string
  selectedDeparture: any
  selectedArrival: any
  frequency: number
  connection: ConnectionStatus
}

const props = defineProps<{
  loading: boolean
}>()

const emit = defineEmits(['submit'])

// --- Options ---
const statuses = [
  { value: 'employee', label: '👔 Salarié' },
  { value: 'student', label: '🎓 Étudiant (-26 ans)' },
  { value: 'student_scholarship', label: '🎓 Étudiant Boursier' },
  { value: 'job_seeker', label: '💼 Demandeur d\'emploi' },
  { value: 'military', label: '🪖 Militaire' },
  { value: 'retired', label: '👴 Retraité' },
]

// --- State ---
const birthDate = ref('2000-05-20')
const status = ref('student_scholarship')

// Liste des trajets (Initialisée avec un trajet vide)
const trips = ref<TripInput[]>([
  {
    id: Date.now(),
    departureName: '',
    arrivalName: '',
    selectedDeparture: null,
    selectedArrival: null,
    frequency: 4,
    // Init avec un tableau vide
    connection: { loading: false, available: null, message: null, transport_types: [] }
  }
])

// --- Computed ---
const isButtonDisabled = computed(() => {
  if (props.loading) return true
  // On vérifie si TOUS les trajets sont valides
  const allTripsValid = trips.value.every(t => 
    t.selectedDeparture?.id_station && 
    t.selectedArrival?.id_station &&
    !t.connection.loading &&
    (t.connection.available !== false) // Bloque si connexion vérifiée et invalide
  )
  return !allTripsValid
})

// --- Logic ---

const addTrip = () => {
  trips.value.push({
    id: Date.now(),
    departureName: '',
    arrivalName: '',
    selectedDeparture: null,
    selectedArrival: null,
    frequency: 2, 
    connection: { loading: false, available: null, message: null, transport_types: [] }
  })
}

const removeTrip = (index: number) => {
  trips.value.splice(index, 1)
}

// Vérification de connexion spécifique à un trajet (index)
const checkConnection = async (index: number) => {
  const trip = trips.value[index]
  
  if (!trip.selectedDeparture?.uic_code || !trip.selectedArrival?.uic_code) return

  trip.connection.loading = true
  trip.connection.available = null

  try {
    const data = await $fetch<any>('http://localhost:3001/stations/check-connection-uic', {
      params: {
        from: trip.selectedDeparture.uic_code,
        to: trip.selectedArrival.uic_code
      }
    })
    
    trip.connection.available = data.is_direct_connection_available
    // MODIFICATION : On récupère le tableau envoyé par le back
    trip.connection.transport_types = data.transport_types || [] 
    trip.connection.message = data.message

  } catch (e) {
    trip.connection.available = false
    trip.connection.message = "❌ Erreur API"
    trip.connection.transport_types = []
  } finally {
    trip.connection.loading = false
  }
}

const onSelectDeparture = (station: any, index: number) => {
  trips.value[index].selectedDeparture = station
  checkConnection(index)
}

const onSelectArrival = (station: any, index: number) => {
  trips.value[index].selectedArrival = station
  checkConnection(index)
}

const handleSubmit = () => {
  // Construction du payload conforme à la nouvelle API
  const payload = {
    birth_date: birthDate.value,
    status: status.value,
    trips: trips.value.map(t => ({
      departure_station_id: t.selectedDeparture.id_station,
      arrival_station_id: t.selectedArrival.id_station,
      frequency: Number(t.frequency)
    }))
  }
  emit('submit', payload)
}
</script>

<template>
  <div class="w-full max-w-6xl bg-slate-900/60 backdrop-blur-md border border-white/5 rounded-[2rem] shadow-2xl overflow-visible animate-fade-in-up">
    <div class="grid grid-cols-1 lg:grid-cols-12 min-h-[450px]">
      
      <div class="lg:col-span-7 p-8 md:p-10 flex flex-col border-b lg:border-b-0 lg:border-r border-white/5">
        <div class="flex justify-between items-center mb-6">
          <h3 class="text-sm font-bold text-slate-400 flex items-center gap-2 uppercase tracking-wider">
            <span class="text-lg grayscale opacity-70">📍</span> Vos Itinéraires
          </h3>
          <button @click="addTrip" class="text-xs font-bold text-emerald-400 hover:text-emerald-300 flex items-center gap-1 transition-colors px-2 py-1 rounded bg-emerald-500/10 border border-emerald-500/20">
            <span>+</span> Ajouter un trajet
          </button>
        </div>
        
        <div class="flex flex-col space-y-8">
          <div 
            v-for="(trip, index) in trips" 
            :key="trip.id"
            class="relative pl-6 border-l-2 border-slate-800 transition-all duration-300"
            :class="{'border-indigo-500': trip.selectedDeparture && trip.selectedArrival}"
          >
            <div class="absolute -left-[9px] top-0 w-4 h-4 rounded-full bg-slate-800 border-2 border-slate-600 flex items-center justify-center"></div>
            
            <div class="flex justify-between items-start mb-2">
              <span class="text-xs font-bold text-slate-500 uppercase">Trajet {{ index + 1 }}</span>
              <button v-if="trips.length > 1" @click="removeTrip(index)" class="text-xs text-red-400 hover:text-red-300 underline">Supprimer</button>
            </div>

            <div class="space-y-3">
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <StationSearch 
                  label="Départ" 
                  v-model="trip.departureName" 
                  @select="(s) => onSelectDeparture(s, index)"
                  placeholder="Gare de départ..."
                  color-focus="indigo"
                />
                <StationSearch 
                  label="Arrivée" 
                  v-model="trip.arrivalName" 
                  @select="(s) => onSelectArrival(s, index)"
                  placeholder="Gare d'arrivée..."
                  color-focus="pink"
                />
              </div>

              <div v-if="trip.connection.loading || trip.connection.message || trip.connection.available" class="text-xs flex items-center gap-2 min-h-[20px]">
                 
                 <span v-if="trip.connection.loading" class="text-indigo-400 flex items-center gap-1">
                   <svg class="animate-spin h-3 w-3" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                   Vérification...
                 </span>

                 <span v-else-if="trip.connection.available" class="flex flex-wrap gap-1.5 items-center">
                   <span class="text-emerald-400 font-medium mr-1">✨ Direct:</span>
                   
                   <span 
                     v-for="mode in trip.connection.transport_types" 
                     :key="mode"
                     class="bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 px-1.5 py-0.5 rounded text-[10px] font-bold uppercase tracking-wide"
                   >
                     {{ mode }}
                   </span>
                 </span>

                 <span v-else class="text-red-400">{{ trip.connection.message }}</span>
              </div>

              <div class="pt-2">
                 <div class="flex justify-between items-end mb-1">
                   <label class="text-[10px] font-bold text-slate-500 uppercase">Fréquence / mois</label>
                   <span class="text-sm font-bold text-slate-200">{{ trip.frequency }} A/R</span>
                 </div>
                 <input 
                   type="range" v-model="trip.frequency" min="1" max="30" step="1"
                   class="w-full h-1.5 bg-slate-800 rounded-full appearance-none cursor-pointer accent-indigo-500 hover:accent-indigo-400"
                 />
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="lg:col-span-5 bg-black/20 p-8 md:p-10 flex flex-col justify-start gap-8 sticky top-0">
         <div>
            <h3 class="text-sm font-bold text-slate-400 mb-6 flex items-center gap-2 uppercase tracking-wider">
              <span class="text-lg grayscale opacity-70">👤</span> Votre Profil
            </h3>

            <div class="space-y-5">
              <div class="bg-slate-900/50 p-1 rounded-xl border border-white/5 focus-within:border-indigo-500/50 transition-colors">
                <BaseInput label="Date de naissance" type="date" v-model="birthDate" />
              </div>
              <div class="bg-slate-900/50 p-1 rounded-xl border border-white/5 focus-within:border-indigo-500/50 transition-colors">
                <BaseSelect label="Statut actuel" v-model="status" :options="statuses" />
              </div>
            </div>
         </div>

         <div class="mt-auto pt-8">
           <button 
              @click="handleSubmit"
              :disabled="isButtonDisabled"
              class="group w-full relative overflow-hidden bg-indigo-600 text-white font-bold py-4 rounded-xl shadow-lg shadow-indigo-900/20 transform transition-all duration-300 hover:bg-indigo-500 active:scale-[0.98] disabled:opacity-50 disabled:cursor-not-allowed disabled:shadow-none disabled:bg-slate-800 disabled:text-slate-500"
           >
              <span v-if="!loading" class="relative z-10 flex items-center justify-center gap-2 text-sm">
                Lancer la simulation ({{ trips.length }} trajets)
                <svg class="w-4 h-4 text-indigo-200" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M13 7l5 5m0 0l-5 5m5-5H6" /></svg>
              </span>
              <span v-else class="relative z-10 flex items-center justify-center gap-2 text-sm">
                <svg class="animate-spin h-4 w-4 text-slate-400" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                Calcul global...
              </span>
           </button>
         </div>
      </div>
    </div>
  </div>
</template>