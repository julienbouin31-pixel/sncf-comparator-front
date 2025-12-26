<template>
  <div class="text-center mb-10 max-w-4xl animate-fade-in-down">
    <div class="inline-flex items-center gap-2 mb-4 px-3 py-1.5 rounded-full bg-slate-900/50 border border-slate-800 backdrop-blur-sm cursor-default">
      <span class="relative flex h-2 w-2">
        <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-500 opacity-75"></span>
        <span class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500"></span>
      </span>
      <span class="text-[10px] font-bold text-slate-400 tracking-wider uppercase">Simulateur V1</span>
    </div>
    
    <h1 class="text-4xl md:text-6xl font-extrabold mb-4 tracking-tight text-white leading-tight">
      Optimisez vos <span class="text-transparent bg-clip-text bg-gradient-to-r from-indigo-400 to-cyan-400">trajets.</span>
    </h1>
    
    <p class="text-base md:text-lg text-slate-400 font-normal max-w-xl mx-auto">
      Comparez instantanément les abonnements <strong class="text-slate-200">SNCF</strong> & <strong class="text-slate-200">TER</strong>.
    </p>
  </div>
</template>

<style scoped>
.animate-fade-in-down { animation: fadeInDown 0.8s ease-out forwards; }
@keyframes fadeInDown {
  from { opacity: 0; transform: translateY(-10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>