<template>
  <div class="bg-white p-8 rounded-[2.5rem] border border-slate-200 shadow-sm h-full">
    <h3 class="text-sm font-black text-slate-400 uppercase tracking-widest mb-6 text-center">{{ title }}</h3>
    <div class="h-[300px]">
      <component :is="chartComponent" :data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js';
import { Pie, Doughnut } from 'vue-chartjs';
import ChartDataLabels from 'chartjs-plugin-datalabels';

ChartJS.register(ArcElement, Tooltip, Legend, ChartDataLabels);

const props = defineProps(['ticketData', 'mode', 'title', 'type'])
const PROFESSIONAL_PALETTE = [
  '#4F46E5', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6', '#EC4899', '#3B82F6', 
  '#22D3EE', '#F472B6', '#14B8A6', '#F97316', '#6366F1', '#A855F7', '#D946EF', 
  '#06B6D4', '#10B981', '#84CC16', '#EAB308', '#F59E0B', '#F97316', '#EF4444', 
  '#64748B', '#475569', '#334155', '#1E293B', '#0F172A', '#94A3B8', '#CBD5E1', 
  '#E2E8F0', '#F1F5F9'
];;

const chartComponent = computed(() => props.type === 'pie' ? Pie : Doughnut);

const chartData = computed(() => {
  const counts = {};
  props.ticketData.forEach(t => {
    const key = t[props.mode] || 'Sin especificar';
    counts[key] = (counts[key] || 0) + 1;
  });

  return {
    labels: Object.keys(counts),
    datasets: [{
      data: Object.values(counts),
      backgroundColor: PROFESSIONAL_PALETTE,
      borderWidth: 0
    }]
  };
});

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { position: 'bottom', labels: { boxWidth: 12, font: { size: 10, weight: 'bold' } } },
    datalabels: {
      color: '#fff',
      font: { weight: 'bold', size: 11 },
      formatter: (value, ctx) => {
        let sum = 0;
        let dataArr = ctx.chart.data.datasets[0].data;
        dataArr.map(data => sum += data);
        let percentage = (value * 100 / sum).toFixed(0) + "%";
        return `${value} (${percentage})`;
      }
    }
  }
};
</script>