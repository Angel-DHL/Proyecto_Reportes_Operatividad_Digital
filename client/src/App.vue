<template>
  <div class="min-h-screen bg-gray-50 p-4 md:p-8">
    <header class="max-w-6xl mx-auto mb-8">
      <h1 class="text-4xl font-extrabold text-slate-900 tracking-tight">
        Operatividad Digital
      </h1>
      <p class="text-slate-500 mt-2">Visualizador de métricas en tiempo real</p>
    </header>

    <div class="max-w-6xl mx-auto space-y-8">
      <FileUploader @data-ready="handleNewData" />

      <div v-if="reportData.length > 0" class="animate-in fade-in duration-500">
        
        <div class="flex items-center gap-4 mb-6">
          <div :class="reportType === 'general' ? 'bg-green-100 text-green-700' : 'bg-blue-100 text-blue-700'" 
               class="px-4 py-2 rounded-full font-bold text-sm uppercase">
            {{ reportType === 'general' ? '📊 Reporte General' : '🏭 Reporte Manufactura' }}
          </div>
          <span class="text-gray-400">|</span>
          <span class="text-gray-600 font-medium">{{ reportData.length }} Tickets encontrados</span>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <StatusChart :ticketData="reportData" />

          <div class="bg-gradient-to-br from-slate-800 to-slate-900 p-6 rounded-xl text-white shadow-xl">
            <h3 class="text-slate-400 uppercase text-xs font-bold mb-4 tracking-widest">Resumen Crítico</h3>
            <div class="space-y-4">
              <div v-for="(val, key) in getKpis" :key="key" class="flex justify-between items-center border-b border-slate-700 pb-2">
                <span class="text-slate-300">{{ key }}</span>
                <span class="text-2xl font-mono font-bold">{{ val }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import FileUploader from './components/FileUploader.vue';
import StatusChart from './components/charts/StatusChart.vue';

const reportData = ref([]);
const reportType = ref('');

const handleNewData = (payload) => {
  reportData.value = payload.rows;
  reportType.value = payload.type;
};

// Pequeña lógica para KPIs rápidos
const getKpis = computed(() => {
  const criticos = reportData.value.filter(t => t.Prioridad?.includes('1 Crítico')).length;
  const abiertos = reportData.value.filter(t => t.Status === 'OPEN' || t.Status === 'ASSIGNED').length;
  const resueltos = reportData.value.filter(t => t.Status === 'CLOSED' || t.Status === 'RESOLVED').length;
  
  return {
    'Prioridad Crítica': criticos,
    'Tickets Pendientes': abiertos,
    'Total Resueltos': resueltos
  };
});
</script>