<template>
  <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-100">
    <h3 class="text-sm font-bold text-gray-400 uppercase tracking-wider mb-4">Distribución por Estatus</h3>
    <div class="h-64">
      <Pie :data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js';
import { Pie } from 'vue-chartjs';

// Registramos los módulos necesarios de Chart.js
ChartJS.register(ArcElement, Tooltip, Legend);

const props = defineProps({
  ticketData: {
    type: Array,
    required: true
  }
});

const chartData = computed(() => {
  const counts = {};
  
  // Contamos las ocurrencias de cada "Status"
  props.ticketData.forEach(ticket => {
    const status = ticket.Status || 'Sin Estatus';
    counts[status] = (counts[status] || 0) + 1;
  });

  return {
    labels: Object.keys(counts),
    datasets: [
      {
        backgroundColor: ['#3B82F6', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6', '#6366F1'],
        data: Object.values(counts),
        borderWidth: 0,
        hoverOffset: 10
      }
    ]
  };
});

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { position: 'bottom', labels: { usePointStyle: true, padding: 20 } }
  }
};
</script>