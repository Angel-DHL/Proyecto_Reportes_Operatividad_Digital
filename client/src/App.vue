<template>
  <div class="min-h-screen bg-[#f8fafc] text-slate-900 pb-20">
    <nav class="bg-[#0f172a] text-white px-8 py-4 sticky top-0 z-50 shadow-2xl">
      <div class="max-w-[1600px] mx-auto flex justify-between items-center">
        <div class="flex items-center gap-5">
          <div class="bg-white p-1 rounded-xl shadow-lg flex items-center justify-center overflow-hidden h-12 w-auto min-w-[50px]">
            <img 
              :src="logoUrl" 
              alt="Logo" 
              class="h-full w-auto object-contain"
              @error="(e) => e.target.src = 'https://api.dicebear.com/7.x/initials/svg?seed=DE&backgroundColor=4f46e5'"
            />
          </div>
          <div>
            <h1 class="text-xl font-black tracking-wider leading-none uppercase">DEACERO ANALYTICS</h1>
            <p class="text-[9px] font-bold text-blue-400 tracking-[0.3em] uppercase mt-1">Inteligencia de Datos Operativa</p>
          </div>
        </div>
        <div v-if="reportData.length">
          <button @click="exportFullPDF" 
            :disabled="isExporting"
            class="bg-blue-600 hover:bg-blue-500 disabled:bg-slate-600 px-6 py-2.5 rounded-xl font-bold text-sm transition-all flex items-center gap-2 shadow-xl hover:scale-105 active:scale-95">
            <Download class="w-4 h-4" /> 
            {{ isExporting ? 'Generando PDF...' : 'Exportar Reporte Ejecutivo (PDF)' }}
          </button>
        </div>
      </div>
    </nav>

    <main id="dashboard-to-print" class="max-w-[1600px] mx-auto p-6 lg:p-10 space-y-12 bg-[#f8fafc]">
      <div v-if="!reportData.length" class="max-w-xl mx-auto py-20">
        <FileUploader @data-ready="handleNewData" />
      </div>

      <div v-else class="animate-in fade-in duration-700 space-y-12">
        
        <div class="flex flex-col md:flex-row justify-between items-end gap-6 border-b border-slate-200 pb-8">
          <div>
            <h2 class="text-4xl font-black text-slate-900 tracking-tight">Dashboard de Operatividad Digital</h2>
            <p class="text-slate-500 font-medium italic">Análisis Integral de Desempeño y Calidad</p>
          </div>
          <div class="bg-white px-6 py-3 rounded-2xl shadow-sm border border-slate-200 text-right">
            <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Período del Reporte</p>
            <p class="text-lg font-black text-blue-600 uppercase">{{ dateRange }}</p>
          </div>
        </div>

        <section class="space-y-6 pdf-section">
          <div class="flex items-center gap-4">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.4em] whitespace-nowrap">I. Resumen Ejecutivo de Operación</h2>
            <div class="h-px bg-slate-200 flex-1"></div>
          </div>
          <div class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-6 gap-6">
            <KPICard title="Total Tickets" :value="reportData.length" color="indigo" :icon="ClipboardList" />
            <KPICard title="Abiertos" :value="metrics.open" color="amber" :icon="Clock" />
            <KPICard title="Resueltos" :value="metrics.resolvedCount" color="emerald" :icon="CheckCircle2" />
            <KPICard title="TTR Promedio" :value="metrics.avgTTR + ' días'" color="emerald" :icon="Timer" />
            <KPICard title="Cumplimiento SLA" :value="metrics.sla + '%'" color="blue" :icon="CheckCircle2" />
            <KPICard title="Prioridad Crítica" :value="metrics.criticals" color="red" :icon="AlertCircle" />
          </div>
        </section>

        <section class="space-y-6 pdf-section">
          <div class="flex items-center gap-4">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.4em] whitespace-nowrap">II. Patrones y Análisis de Riesgo</h2>
            <div class="h-px bg-slate-200 flex-1"></div>
          </div>
          <div class="bg-[#1e1b4b] rounded-[2.5rem] p-10 text-white shadow-2xl relative overflow-hidden">
            <Zap class="absolute -right-10 -top-10 w-64 h-64 text-white/5 rotate-12" />
            <div class="grid grid-cols-1 md:grid-cols-3 gap-8 text-center md:text-left">
              <div class="space-y-2 border-slate-700/50 md:border-r pr-4">
                <p class="text-xs font-bold text-indigo-300 uppercase tracking-widest">Riesgo de SLA</p>
                <p class="text-3xl font-black">{{ metrics.slaRisk }} Tickets</p>
              </div>
              <div class="space-y-2 border-slate-700/50 md:border-r pr-4">
                <p class="text-xs font-bold text-indigo-300 uppercase tracking-widest">Tópicos Recurrentes</p>
                <div class="flex flex-wrap gap-2 justify-center md:justify-start">
                  <span v-for="t in metrics.topics" :key="t" class="bg-white/10 px-2 py-1 rounded text-[10px] font-bold uppercase">#{{ t }}</span>
                </div>
              </div>
              <div class="space-y-2">
                <p class="text-xs font-bold text-indigo-300 uppercase tracking-widest">Sistema Crítico</p>
                <p class="text-xl font-black text-amber-400 truncate">{{ metrics.mostCriticalSystem }}</p>
              </div>
            </div>
          </div>
        </section>

        <section class="space-y-6 pdf-section">
          <div class="flex items-center gap-4">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.4em] whitespace-nowrap">III. Métricas de Desempeño y Calidad</h2>
            <div class="h-px bg-slate-200 flex-1"></div>
          </div>
          <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
            <AgentChart :ticketData="reportData" mode="Fecha vencimiento" title="Proyección de Vencimientos" type="line" />
            <AgentChart :ticketData="reportData" mode="dayOfWeek" title="Tendencia Semanal de Carga" type="bar" multiColor />
          </div>
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
            <StatusChart :ticketData="reportData" mode="issuetype" title="Naturaleza de la Demanda" type="pie" />
            <AgentChart :ticketData="reportData" mode="Causa de cierre" title="Causas de Cierre" type="bar" multiColor />
            <StatusChart :ticketData="reportData" mode="Status" title="Estatus de Calidad" type="doughnut" />
          </div>
        </section>

        <section class="space-y-6 pdf-section">
          <div class="flex items-center gap-4">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.4em] whitespace-nowrap">IV. Análisis Operativo</h2>
            <div class="h-px bg-slate-200 flex-1"></div>
          </div>
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
            <AgentChart :ticketData="reportData" mode="Prioridad" title="Volumen por Prioridad" type="bar" multiColor />
            <StatusChart :ticketData="reportData" mode="Ubicacion" title="Distribución Geográfica" type="pie" />
            <AgentChart :ticketData="reportData" mode="Sistema" title="Tickets por Sistema" type="bar" multiColor />
          </div>
        </section>

        <section class="space-y-6 pdf-section">
          <div class="flex items-center gap-4">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.4em] whitespace-nowrap">V. Carga de Trabajo</h2>
            <div class="h-px bg-slate-200 flex-1"></div>
          </div>
          <AgentChart :ticketData="reportData" mode="Agente" title="Top 10 Agentes" :limit="10" type="bar" multiColor />
        </section>

        <section class="space-y-6 pdf-section pdf-table">
          <div class="flex items-center gap-4">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.4em] whitespace-nowrap">VI. Detalle de Tickets</h2>
            <div class="h-px bg-slate-200 flex-1"></div>
          </div>
          <TicketTable :tickets="reportData" class="ticket-table w-full"/>
        </section>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { Download, ClipboardList, Timer, CheckCircle2, AlertCircle, Clock, Zap } from 'lucide-vue-next';
import { differenceInDays, parse, format } from 'date-fns';
import enUS from 'date-fns/locale/en-US';
import { toJpeg } from 'html-to-image';
import jsPDF from 'jspdf';

import logoUrl from './assets/logo.png';
import FileUploader from './components/FileUploader.vue';
import KPICard from './components/KPICard.vue';
import StatusChart from './components/charts/StatusChart.vue';
import AgentChart from './components/charts/AgentChart.vue';
import TicketTable from './components/TicketTable.vue';

const reportData = ref([]);
const isExporting = ref(false);

/* ===================== DATA ===================== */

const handleNewData = (p) => { 
  reportData.value = p.rows.map(row => {
    if (row.creado) {
      const d = parse(row.creado, 'MMM d, yyyy', new Date(), { locale: enUS });
      row.dayOfWeek = format(d, 'EEEE', { locale: enUS });
    }
    return row;
  });
};

const dateRange = computed(() => {
  if (!reportData.value.length) return '';
  const dates = reportData.value
    .map(t => t.creado ? parse(t.creado, 'MMM d, yyyy', new Date(), { locale: enUS }) : null)
    .filter(Boolean)
    .sort((a,b) => a-b);
  return `${format(dates[0], 'dd MMM yyyy')} - ${format(dates.at(-1), 'dd MMM yyyy')}`;
});

const metrics = computed(() => {
  const data = reportData.value;
  if (!data.length) return {};

  const resolved = data.filter(t => ['CLOSED','RESOLVED'].includes(t.Status.toUpperCase()));
  const totalDays = resolved.reduce((a,t) =>
    a + Math.abs(differenceInDays(
      parse(t.Resuelto,'MMM d, yyyy',new Date(),{locale:enUS}),
      parse(t.creado,'MMM d, yyyy',new Date(),{locale:enUS})
    )),0);

  const criticals = data.filter(t => t.Prioridad?.includes('1') && !resolved.includes(t));

  return {
    open: data.length - resolved.length,
    resolvedCount: resolved.length,
    avgTTR: resolved.length ? (totalDays / resolved.length).toFixed(1) : 0,
    sla: ((resolved.length / data.length) * 100).toFixed(0),
    criticals: criticals.length,
    slaRisk: criticals.length,
    topics: [...new Set(data.map(t => t.Detalle).join(' ').split(/\W+/).filter(w => w.length > 5))].slice(0,3),
    mostCriticalSystem: criticals[0]?.Sistema || 'N/A'
  };
});

/* ===================== PDF ===================== */

const exportFullPDF = async () => {
  if (isExporting.value) return;
  isExporting.value = true;

  try {
    const pdf = new jsPDF('l', 'mm', 'a4');
    const pageWidth = 297;
    const pageHeight = 210;

    const sections = document.querySelectorAll('.pdf-section');

    for (const section of sections) {
      // 🔴 SALTAMOS LA TABLA
      if (section.classList.contains('pdf-table')) continue;

      const imgData = await toJpeg(section, {
        pixelRatio: 2,
        backgroundColor: '#f8fafc',
      });

      const img = new Image();
      img.src = imgData;
      await new Promise(r => img.onload = r);

      const imgHeight = (img.height * pageWidth) / img.width;
      pdf.addPage();
      pdf.addImage(imgData, 'JPEG', 0, 0, pageWidth, imgHeight);
    }

    // 🔥 TABLA PAGINADA CORRECTAMENTE
    await exportTicketTable(pdf);

    pdf.deletePage(1); // elimina página en blanco inicial
    pdf.save(`Reporte_Operatividad_${Date.now()}.pdf`);

  } catch (e) {
    console.error(e);
    alert('Error al generar PDF');
  } finally {
    isExporting.value = false;
  }
};

/* ===================== TABLE PAGINATION ===================== */

const exportTicketTable = async (pdf) => {
  const table = document.querySelector('.pdf-table table');
  if (!table) return;

  const rows = [...table.querySelectorAll('tbody tr')];
  const header = table.querySelector('thead');
  const rowsPerPage = 12;
  const pageWidth = 297;

  for (let i = 0; i < rows.length; i += rowsPerPage) {
    const wrapper = document.createElement('div');
    wrapper.style.padding = '20px';
    wrapper.style.background = '#f8fafc';

    const t = table.cloneNode(false);
    t.appendChild(header.cloneNode(true));

    const tbody = document.createElement('tbody');
    rows.slice(i, i + rowsPerPage).forEach(r => tbody.appendChild(r.cloneNode(true)));
    t.appendChild(tbody);

    wrapper.appendChild(t);
    document.body.appendChild(wrapper);

    const imgData = await toJpeg(wrapper, {
      pixelRatio: 2,
      backgroundColor: '#f8fafc',
    });

    document.body.removeChild(wrapper);

    const img = new Image();
    img.src = imgData;
    await new Promise(r => img.onload = r);

    const imgHeight = (img.height * pageWidth) / img.width;
    pdf.addPage();
    pdf.addImage(imgData, 'JPEG', 0, 10, pageWidth, imgHeight);
  }
};
</script>