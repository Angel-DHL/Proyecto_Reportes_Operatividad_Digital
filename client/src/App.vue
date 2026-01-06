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

        <section class="space-y-6 pdf-section">
          <div class="flex items-center gap-4">
            <h2 class="text-xs font-black text-slate-400 uppercase tracking-[0.4em] whitespace-nowrap">VI. Detalle de Tickets</h2>
            <div class="h-px bg-slate-200 flex-1"></div>
          </div>
          <TicketTable :tickets="reportData" />
        </section>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { LayoutDashboard, Download, ClipboardList, Timer, CheckCircle2, AlertCircle, Clock, Zap } from 'lucide-vue-next';
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

const handleNewData = (p) => { 
  const rows = p.rows.map(row => {
    if (row.creado) {
      const date = parse(row.creado, 'MMM d, yyyy', new Date(), { locale: enUS });
      row.dayOfWeek = format(date, 'EEEE', { locale: enUS });
    }
    return row;
  });
  reportData.value = rows; 
};

const dateRange = computed(() => {
  if (!reportData.value.length) return '';
  const parseD = (s) => s ? parse(s, 'MMM d, yyyy', new Date(), { locale: enUS }) : null;
  const dates = reportData.value.map(t => parseD(t.creado)).filter(d => d).sort((a,b) => a-b);
  return dates.length ? `${format(dates[0], 'dd MMM yyyy')} - ${format(dates[dates.length-1], 'dd MMM yyyy')}` : 'N/A';
});

const metrics = computed(() => {
  const data = reportData.value;
  if (!data.length) return { avgTTR: 0, sla: 0, criticals: 0, open: 0, resolvedCount: 0, slaRisk: 0, topics: [], mostCriticalSystem: 'N/A' };

  const parseD = (s) => s ? parse(s, 'MMM d, yyyy', new Date(), { locale: enUS }) : null;
  const resolvedArr = data.filter(t => t.Resuelto && t.creado);
  const totalDays = resolvedArr.reduce((acc, t) => acc + Math.abs(differenceInDays(parseD(t.Resuelto), parseD(t.creado))), 0);
  
  const words = data.map(t => t.Detalle).join(' ').toLowerCase().split(/\W+/);
  const topics = [...new Set(words.filter(w => w.length > 5))].slice(0, 3);
  const sysCounts = {};
  data.filter(t => t.Prioridad.includes('1')).forEach(t => sysCounts[t.Sistema] = (sysCounts[t.Sistema] || 0) + 1);

  return {
    open: data.filter(t => !['CLOSED', 'RESOLVED'].includes(t.Status.toUpperCase())).length,
    resolvedCount: data.filter(t => ['CLOSED', 'RESOLVED'].includes(t.Status.toUpperCase())).length,
    avgTTR: resolvedArr.length ? (totalDays / resolvedArr.length).toFixed(1) : 0,
    sla: ((data.filter(t => ['CLOSED', 'RESOLVED'].includes(t.Status.toUpperCase())).length / data.length) * 100).toFixed(0),
    criticals: data.filter(t => t.Prioridad.includes('1') && !['CLOSED','RESOLVED'].includes(t.Status)).length,
    slaRisk: data.filter(t => t.Prioridad.includes('1') && !['CLOSED','RESOLVED'].includes(t.Status)).length,
    topics,
    mostCriticalSystem: Object.keys(sysCounts).sort((a,b) => sysCounts[b] - sysCounts[a])[0] || 'N/A'
  };
});

const exportFullPDF = async () => {
  if (isExporting.value) return;
  isExporting.value = true;

  try {
    const element = document.getElementById('dashboard-to-print');
    if (!element) throw new Error('Elemento no encontrado');

    // 1. Pausa para estabilizar gráficos
    await new Promise(r => setTimeout(r, 1000));

    // 2. Captura con eliminación de márgenes CSS
    const dataUrl = await toJpeg(element, {
      pixelRatio: 2,
      quality: 0.95,
      backgroundColor: '#f8fafc',
      // FORZAMOS ELIMINACIÓN DE PADDING/MARGIN INTERNO
      style: {
        padding: '0',
        margin: '0',
        maxWidth: 'none',
        width: element.scrollWidth + 'px'
      }
    });

    // 3. Configuración de PDF Horizontal A4 (297 x 210 mm)
    const pdf = new jsPDF('l', 'mm', 'a4');
    const pageWidth = 297; 
    const pageHeight = 210;

    const img = new Image();
    img.src = dataUrl;
    await new Promise(resolve => (img.onload = resolve));

    // Calculamos dimensiones para que el ancho sea EXACTAMENTE el de la hoja
    const imgWidth = pageWidth;
    const imgHeight = (img.height * imgWidth) / img.width;

    let heightLeft = imgHeight;
    let position = 0;

    // 4. Inserción de imagen en la coordenada X = 0 (Borde Izquierdo)
    // El primer '0' es la coordenada X, el segundo 'position' es Y
    pdf.addImage(dataUrl, 'JPEG', 10, position, imgWidth, imgHeight);
    heightLeft -= pageHeight;

    // Paginación si el contenido excede el alto de la hoja
    while (heightLeft > 0) {
      position = heightLeft - imgHeight;
      pdf.addPage();
      // Nuevamente X = 0 para eliminar margen izquierdo en páginas siguientes
      pdf.addImage(dataUrl, 'JPEG', 10, position, imgWidth, imgHeight);
      heightLeft -= pageHeight;
    }

    pdf.save(`Reporte_Operatividad_FullWidth_${Date.now()}.pdf`);
    
  } catch (err) {
    console.error('Error en PDF Full Width:', err);
    alert('No se pudo generar el PDF sin márgenes.');
  } finally {
    isExporting.value = false;
  }
};
</script>