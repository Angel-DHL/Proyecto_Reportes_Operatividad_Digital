<template>
  <div class="bg-white p-8 rounded-[2.5rem] border border-slate-200 shadow-sm h-full">
    <h3 class="text-sm font-black text-slate-400 uppercase tracking-widest mb-6">{{ title }}</h3>
    <div class="h-[300px]">
      <component :is="chartComponent" :data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';
import { 
  Chart as ChartJS, Title, Tooltip, Legend, BarElement, 
  LineElement, PointElement, CategoryScale, LinearScale, Filler 
} from 'chart.js';
import { Bar, Line } from 'vue-chartjs';

ChartJS.register(Title, Tooltip, Legend, BarElement, LineElement, PointElement, CategoryScale, LinearScale, Filler);

const props = defineProps(['ticketData', 'mode', 'title', 'type', 'limit', 'multiColor']);
const PROFESSIONAL_PALETTE = [
  '#4F46E5', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6', '#EC4899', '#3B82F6', 
  '#22D3EE', '#F472B6', '#14B8A6', '#F97316', '#6366F1', '#A855F7', '#D946EF', 
  '#06B6D4', '#10B981', '#84CC16', '#EAB308', '#F59E0B', '#F97316', '#EF4444', 
  '#64748B', '#475569', '#334155', '#1E293B', '#0F172A', '#94A3B8', '#CBD5E1', 
  '#E2E8F0', '#F1F5F9'
];
const chartComponent = computed(() => props.type === 'line' ? Line : Bar);

const chartData = computed(() => {
  const counts = {};
  props.ticketData.forEach(t => {
    const key = t[props.mode] || 'N/A';
    if (key !== 'N/A') {
      counts[key] = (counts[key] || 0) + 1;
    }
  });

  let labels = Object.keys(counts);
  
  if (props.type === 'line') {
    // Ordenar fechas cronológicamente para la proyección
    labels.sort((a, b) => new Date(a) - new Date(b));
  } else {
    // Ordenar por volumen para las causas de cierre, agentes, etc.
    labels.sort((a, b) => counts[b] - counts[a]);
    if (props.limit) labels = labels.slice(0, props.limit);
  }

  const values = labels.map(l => counts[l]);
  const palette = ['#4F46E5', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6', '#EC4899', '#3B82F6', '#22D3EE', '#F472B6', '#14B8A6'];

  return {
    labels,
    datasets: [{
      label: 'Cantidad de Tickets',
      data: values,
      backgroundColor: props.type === 'bar' 
        ? labels.map((_, i) => PROFESSIONAL_PALETTE[i % PROFESSIONAL_PALETTE.length]) 
        : '#4F46E5',
      borderColor: '#4F46E5',
      borderWidth: 3,
      tension: 0.4,
      fill: props.type === 'line',
      borderRadius: 10,
      pointRadius: props.type === 'line' ? 4 : 0,
      pointBackgroundColor: '#4F46E5'
    }]
  };
});

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: { 
    legend: { display: false },
    tooltip: {
      backgroundColor: '#0f172a',
      padding: 12,
      cornerRadius: 8
    }
  },
  scales: {
    y: { beginAtZero: true, grid: { color: '#f1f5f9' }, ticks: { font: { weight: 'bold' } } },
    x: { grid: { display: false }, ticks: { font: { size: 10, weight: '600' } } }
  }
};
</script>