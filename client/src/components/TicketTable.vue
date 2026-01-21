<template>
  <div class="bg-white rounded-3xl shadow-sm border border-slate-200 overflow-hidden">
    <div class="p-6 border-b border-slate-100 flex flex-col md:flex-row justify-between items-center gap-4 bg-slate-50/50">
      <h3 class="font-black text-slate-800 uppercase tracking-tight">Resumen Detallado de Tickets</h3>
      <input 
        v-model="search" 
        type="text" 
        placeholder="Buscar por ID, Agente, Sistema..." 
        class="w-full md:w-80 px-4 py-2 bg-white border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none transition-all shadow-inner"
      />
    </div>
    <div class="overflow-x-auto">
      <table class="w-full text-left text-sm border-collapse">
        <thead>
          <tr class="bg-slate-100/50 text-slate-500 text-[10px] font-black uppercase tracking-[0.15em]">
            <th class="px-6 py-4">Ticket ID</th>
            <th class="px-6 py-4">Detalle</th>
            <th class="px-6 py-4">Estatus</th>
            <th class="px-6 py-4">Prioridad</th>
            <th class="px-6 py-4">Agente</th>
            <th class="px-6 py-4">Solicitante</th>
            <th class="px-6 py-4">Sistema</th>
          </tr>
        </thead>
        <tbody class="divide-y divide-slate-100">
          <tr v-for="ticket in filteredTickets" :key="ticket.key" class="hover:bg-slate-50/80 transition-colors group">
            <td class="px-6 py-4 font-mono font-bold text-indigo-600 whitespace-nowrap">{{ ticket.key }}</td>
            <td class="px-6 py-4 max-w-xs"><p class="truncate text-slate-600" :title="ticket.Detalle">{{ ticket.Detalle }}</p></td>
            <td class="px-6 py-4">
              <span :class="statusClass(ticket.Status)" class="px-2.5 py-1 rounded-full text-[10px] font-bold uppercase tracking-wider">
                {{ ticket.Status }}
              </span>
            </td>
            <td class="px-6 py-4">
              <span :class="priorityClass(ticket.Prioridad)" class="px-2.5 py-1 rounded-full text-[10px] font-bold">
                {{ ticket.Prioridad }}
              </span>
            </td>
            <td class="px-6 py-4 text-slate-500 whitespace-nowrap">{{ ticket.Agente || 'N/A' }}</td>
            <td class="px-6 py-4 text-slate-500 whitespace-nowrap">{{ ticket.Solicitante || 'N/A' }}</td>
            <td class="px-6 py-4 font-medium text-slate-700 whitespace-nowrap">{{ ticket.Sistema || 'N/A' }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
const props = defineProps({ tickets: { type: Array, default: () => [] } });
const search = ref('');

const filteredTickets = computed(() => {
  return props.tickets.filter(t => 
    Object.values(t).some(v => String(v).toLowerCase().includes(search.value.toLowerCase()))
  );
});

const statusClass = (s) => {
  const status = s?.toUpperCase() || '';
  if (['CLOSED', 'RESOLVED', 'DONE'].includes(status)) return 'bg-emerald-100 text-emerald-700';
  if (['OPEN', 'IN PROGRESS', 'ASSIGNED'].includes(status)) return 'bg-blue-100 text-blue-700';
  return 'bg-amber-100 text-amber-700';
};

const priorityClass = (p) => {
  if (p?.includes('1')) return 'bg-red-100 text-red-600';
  if (p?.includes('2')) return 'bg-orange-100 text-orange-600';
  return 'bg-slate-100 text-slate-600';
};
</script>