<template>
  <div class="bg-white p-6 rounded-3xl border border-slate-100 shadow-sm flex items-center justify-between transition-all hover:shadow-md">
    <div class="space-y-1">
      <p class="text-[10px] font-black text-slate-400 uppercase tracking-[0.2em]">{{ title }}</p>
      <p class="text-3xl font-black text-slate-900 leading-none">{{ value }}</p>
    </div>
    <div :class="colorClass" class="w-14 h-14 rounded-2xl flex items-center justify-center shadow-inner">
      <component :is="icon" class="w-7 h-7" />
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';
const props = defineProps(['title', 'value', 'icon', 'color']);

const colorClass = computed(() => {
  const map = {
    indigo: 'bg-indigo-50 text-indigo-600',
    red: 'bg-red-50 text-red-600',
    amber: 'bg-amber-50 text-amber-600',
    emerald: 'bg-emerald-50 text-emerald-600'
  };
  return map[props.color] || map.indigo;
});
</script>