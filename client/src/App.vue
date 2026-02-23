<template>
  <div class="app-root">
    <!-- =========== BACKGROUND GRID =========== -->
    <div class="bg-grid"></div>

    <!-- =========== NAVBAR =========== -->
    <nav class="main-nav">
      <div class="nav-inner">
        <div class="nav-brand">
          <div class="logo-box">
            <svg xmlns="http://www.w3.org/2000/svg" class="logo-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
              <path d="M12 2L2 7l10 5 10-5-10-5z"/><path d="M2 17l10 5 10-5"/><path d="M2 12l10 5 10-5"/>
            </svg>
          </div>
          <div>
            <h1 class="brand-title">DEACERO<span>ANALYTICS</span></h1>
            <p class="brand-sub">
              <span class="pulse-dot"></span>
              Centro de Inteligencia Operativa
            </p>
          </div>
        </div>
        <div class="nav-actions">
          <div v-if="hasData" class="nav-stats">
            <span class="nav-stat"><strong>{{ reportData.length }}</strong> tickets</span>
            <span class="nav-divider">|</span>
            <span class="nav-stat live-indicator">● En vivo</span>
          </div>
          <button v-if="hasData" class="btn-ghost" @click="resetData">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" y1="3" x2="12" y2="15"/></svg>
            Nuevo CSV
          </button>
          <button v-if="hasData" class="btn-export" :disabled="isExporting" @click="exportFullPDF">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg>
            {{ isExporting ? 'Generando…' : 'Exportar PDF Ejecutivo' }}
          </button>
        </div>
      </div>
      <div class="nav-glow"></div>
    </nav>

    <!-- =========== UPLOAD SCREEN =========== -->
    <div v-if="!hasData" class="upload-screen">
      <div class="upload-card">
        <div class="upload-visual">
          <div class="orbit-ring r1"></div>
          <div class="orbit-ring r2"></div>
          <div class="orbit-ring r3"></div>
          <div class="upload-center-icon">
            <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M14.5 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V7.5L14.5 2z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/><polyline points="10 9 9 9 8 9"/></svg>
          </div>
        </div>
        <h2 class="upload-title">Importar Datos Operativos</h2>
        <p class="upload-sub">Arrastra tu archivo CSV o selecciónalo manualmente para iniciar el análisis</p>
        <label class="drop-zone" :class="{ active: dragging }"
          @dragover.prevent="dragging = true" @dragleave="dragging = false" @drop.prevent="handleDrop">
          <div class="drop-inner">
            <svg xmlns="http://www.w3.org/2000/svg" width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" y1="3" x2="12" y2="15"/></svg>
            <span class="drop-text">Haz clic o arrastra un archivo .csv</span>
            <span class="drop-hint">Formatos: CSV con delimitador coma o punto y coma</span>
          </div>
          <input type="file" accept=".csv" class="sr-only" @change="handleFileInput" />
        </label>
        <div class="upload-features">
          <div class="uf"><span class="uf-dot d1"></span>Análisis automático</div>
          <div class="uf"><span class="uf-dot d2"></span>Gráficas interactivas</div>
          <div class="uf"><span class="uf-dot d3"></span>Exportación PDF</div>
        </div>
      </div>
    </div>

    <!-- =========== DASHBOARD =========== -->
    <main v-else class="dash-main">

      <!-- HEADER -->
      <header class="dash-header" ref="secHeader">
        <div>
          <div class="header-eyebrow">Dashboard Operativo</div>
          <h2 class="dash-title">Análisis de Desempeño<br><span>y Calidad Integral</span></h2>
        </div>
        <div class="header-right">
          <div class="period-badge">
            <span class="period-icon">
              <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>
            </span>
            <div>
              <span class="period-label">Período</span>
              <span class="period-value">{{ dateRange }}</span>
            </div>
          </div>
        </div>
      </header>

      <!-- ──── I. KPI CARDS ──── -->
      <section ref="secKPI" class="dash-section pdf-section">
        <div class="sec-head">
          <span class="sec-num">01</span>
          <h3 class="sec-title">Resumen Ejecutivo</h3>
          <div class="sec-line"></div>
        </div>
        <div class="kpi-grid">
          <div v-for="k in kpiCards" :key="k.label" class="kpi-card" :style="{'--kpi-color': k.color, '--kpi-bg': k.bg}">
            <div class="kpi-header">
              <span class="kpi-label">{{ k.label }}</span>
              <div class="kpi-icon-box" v-html="k.svg"></div>
            </div>
            <div class="kpi-body">
              <p class="kpi-value">{{ k.value }}</p>
              <p class="kpi-sub" v-if="k.sub">{{ k.sub }}</p>
            </div>
            <div class="kpi-progress">
              <div class="kpi-progress-track">
                <div class="kpi-progress-fill" :style="{width: k.pct + '%'}"></div>
              </div>
              <span class="kpi-pct">{{ k.pct }}%</span>
            </div>
          </div>
        </div>
      </section>

      <!-- ──── II. RISK ──── -->
      <section ref="secRisk" class="dash-section pdf-section">
        <div class="sec-head">
          <span class="sec-num">02</span>
          <h3 class="sec-title">Análisis de Riesgo</h3>
          <div class="sec-line"></div>
        </div>
        <div class="risk-panel">
          <div class="risk-decoration">
            <div class="risk-circle c1"></div>
            <div class="risk-circle c2"></div>
          </div>
          <div class="risk-grid">
            <div class="risk-card rc1">
              <div class="risk-card-header">
                <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>
                <span>Riesgo SLA</span>
              </div>
              <p class="risk-value">{{ metrics.slaRisk }}</p>
              <p class="risk-desc">Tickets en zona de riesgo crítico</p>
            </div>
            <div class="risk-card rc2">
              <div class="risk-card-header">
                <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polygon points="13 2 3 14 12 14 11 22 21 10 12 10 13 2"/></svg>
                <span>Tópicos Frecuentes</span>
              </div>
              <div class="risk-tags">
                <span v-for="t in metrics.topics" :key="t" class="risk-tag">{{ t }}</span>
                <span v-if="!metrics.topics?.length" class="risk-na">Sin tópicos</span>
              </div>
            </div>
            <div class="risk-card rc3">
              <div class="risk-card-header">
                <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="2" y="3" width="20" height="14" rx="2" ry="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/></svg>
                <span>Sistema Crítico</span>
              </div>
              <p class="risk-system">{{ metrics.mostCriticalSystem }}</p>
            </div>
          </div>
        </div>
      </section>

      <!-- ──── III. CHARTS SET 1 ──── -->
      <section ref="secCharts1" class="dash-section pdf-section">
        <div class="sec-head">
          <span class="sec-num">03</span>
          <h3 class="sec-title">Métricas de Desempeño</h3>
          <div class="sec-line"></div>
        </div>
        <div class="charts-2col">
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Proyección de Vencimientos</h4>
              <span class="chart-badge badge-cyan">Línea temporal</span>
            </div>
            <div class="chart-body"><Line :data="chartVencimientos" :options="lineOpts" /></div>
          </div>
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Carga Semanal</h4>
              <span class="chart-badge badge-violet">Distribución</span>
            </div>
            <div class="chart-body"><Bar :data="chartSemanal" :options="barOpts" /></div>
          </div>
        </div>
      </section>

      <!-- ──── IV. CHARTS SET 2 ──── -->
      <section ref="secCharts2" class="dash-section pdf-section">
        <div class="sec-head">
          <span class="sec-num">04</span>
          <h3 class="sec-title">Análisis de Demanda</h3>
          <div class="sec-line"></div>
        </div>
        <div class="charts-3col">
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Tipos de Incidencia</h4>
              <span class="chart-badge badge-pink">Polar</span>
            </div>
            <div class="chart-body chart-sq"><PolarArea :data="chartDemanda" :options="polarOpts" /></div>
          </div>
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Causas de Cierre</h4>
              <span class="chart-badge badge-amber">Horizontal</span>
            </div>
            <div class="chart-body chart-sq"><Bar :data="chartCierre" :options="barHOpts" /></div>
          </div>
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Estado de Tickets</h4>
              <span class="chart-badge badge-green">Dona</span>
            </div>
            <div class="chart-body chart-sq"><Doughnut :data="chartEstatus" :options="doughnutOpts" /></div>
          </div>
        </div>
      </section>

      <!-- ──── V. CHARTS SET 3 ──── -->
      <section ref="secCharts3" class="dash-section pdf-section">
        <div class="sec-head">
          <span class="sec-num">05</span>
          <h3 class="sec-title">Análisis Operativo</h3>
          <div class="sec-line"></div>
        </div>
        <div class="charts-3col">
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Prioridades</h4>
              <span class="chart-badge badge-red">Distribución</span>
            </div>
            <div class="chart-body chart-sq"><Doughnut :data="chartPrioridad" :options="doughnutOpts" /></div>
          </div>
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Ubicaciones</h4>
              <span class="chart-badge badge-cyan">Geográfico</span>
            </div>
            <div class="chart-body chart-sq"><PolarArea :data="chartUbicacion" :options="polarOpts" /></div>
          </div>
          <div class="chart-card">
            <div class="chart-card-head">
              <h4>Sistemas</h4>
              <span class="chart-badge badge-violet">Volumen</span>
            </div>
            <div class="chart-body chart-sq"><Bar :data="chartSistema" :options="barOpts" /></div>
          </div>
        </div>
      </section>

      <!-- ──── VI. AGENTS ──── -->
      <section ref="secAgentes" class="dash-section pdf-section">
        <div class="sec-head">
          <span class="sec-num">06</span>
          <h3 class="sec-title">Carga por Agente</h3>
          <div class="sec-line"></div>
        </div>
        <div class="chart-card">
          <div class="chart-card-head">
            <h4>Top 10 Agentes con Mayor Carga</h4>
            <span class="chart-badge badge-cyan">Ranking</span>
          </div>
          <div class="chart-body chart-wide"><Bar :data="chartAgentes" :options="barHOpts" /></div>
        </div>
      </section>

      <!-- ──── VII. TABLE ──── -->
      <section ref="secTable" class="dash-section pdf-section pdf-table-section">
        <div class="sec-head">
          <span class="sec-num">07</span>
          <h3 class="sec-title">Detalle de Tickets</h3>
          <div class="sec-line"></div>
        </div>
        <div class="table-toolbar">
          <div class="search-box">
            <svg xmlns="http://www.w3.org/2000/svg" width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
            <input v-model="tableSearch" placeholder="Buscar por ticket, agente, sistema…" class="search-input"/>
          </div>
          <div class="toolbar-right">
            <div class="status-legend">
              <span class="legend-item"><span class="legend-dot dot-open"></span>Abierto</span>
              <span class="legend-item"><span class="legend-dot dot-progress"></span>En Progreso</span>
              <span class="legend-item"><span class="legend-dot dot-resolved"></span>Resuelto</span>
              <span class="legend-item"><span class="legend-dot dot-closed"></span>Cerrado</span>
            </div>
            <span class="table-count">{{ filteredTickets.length }} registros</span>
          </div>
        </div>
        <div class="table-container">
          <table class="data-table">
            <thead>
              <tr>
                <th v-for="col in tableCols" :key="col.key" @click="sortTable(col.key)" class="th-sort">
                  {{ col.label }}
                  <span v-if="sortKey === col.key" class="sort-indicator">{{ sortDir === 'asc' ? '↑' : '↓' }}</span>
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(t, i) in paginatedTickets" :key="i" :class="rowClass(t)">
                <td class="td-key">{{ getField(t, 'Key') }}</td>
                <td><span class="chip" :class="statusChip(getField(t,'Status'))">{{ getField(t,'Status') }}</span></td>
                <td><span class="chip" :class="prioChip(getField(t,'Prioridad'))">{{ getField(t,'Prioridad') }}</span></td>
                <td>{{ getField(t, 'issuetype') }}</td>
                <td>{{ getField(t, 'Sistema') }}</td>
                <td>{{ getField(t, 'Agente') }}</td>
                <td>{{ getField(t, 'Ubicacion') }}</td>
                <td class="td-date">{{ getField(t, 'creado') }}</td>
                <td class="td-date">{{ getField(t, 'Resuelto') }}</td>
              </tr>
              <tr v-if="!paginatedTickets.length"><td :colspan="tableCols.length" class="td-empty">Sin resultados</td></tr>
            </tbody>
          </table>
        </div>
        <div class="pagination">
          <button class="pg-btn" :disabled="currentPage <= 1" @click="currentPage--">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="15 18 9 12 15 6"/></svg>
            Anterior
          </button>
          <div class="pg-numbers">
            <button v-for="p in visiblePages" :key="p" class="pg-num" :class="{ active: p === currentPage }" @click="currentPage = p">{{ p }}</button>
          </div>
          <button class="pg-btn" :disabled="currentPage >= totalPages" @click="currentPage++">
            Siguiente
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="9 18 15 12 9 6"/></svg>
          </button>
        </div>
      </section>
    </main>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

// Charts
import { Bar, Line, Pie, Doughnut, PolarArea } from 'vue-chartjs'
import { Chart, registerables } from 'chart.js'
Chart.register(...registerables)

// Utils
import Papa from 'papaparse'
import { differenceInDays, parse, format } from 'date-fns'
import enUS from 'date-fns/locale/en-US'
import { toJpeg } from 'html-to-image'
import jsPDF from 'jspdf'

// Logo
let logoUrl = ''
try { logoUrl = new URL('./assets/logo.png', import.meta.url).href } catch {}

/* ================================================================
   STATE
   ================================================================ */
const reportData = ref([])
const columnKeys = ref({})
const isExporting = ref(false)
const dragging = ref(false)
const tableSearch = ref('')
const sortKey = ref('')
const sortDir = ref('asc')
const currentPage = ref(1)
const ROWS_PER_PAGE = 14

const hasData = computed(() => reportData.value.length > 0)

// Section refs for PDF
const secHeader = ref(null)
const secKPI = ref(null)
const secRisk = ref(null)
const secCharts1 = ref(null)
const secCharts2 = ref(null)
const secCharts3 = ref(null)
const secAgentes = ref(null)
const secTable = ref(null)

/* ================================================================
   FIELD RESOLVER — fixes the "—" issue
   ================================================================ */
const FIELD_ALIASES = {
  Key:       ['key','ticket','id','clave','número','numero','issue key','llave'],
  Status:    ['status','estado','estatus','state'],
  Prioridad: ['prioridad','priority','prio','nivel'],
  issuetype: ['issuetype','issue type','tipo','type','tipo de incidencia','issue_type'],
  Sistema:   ['sistema','system','aplicación','aplicacion','app','module','módulo','modulo'],
  Agente:    ['agente','agent','asignado','assignee','responsable','assigned','owner'],
  Ubicacion: ['ubicacion','ubicación','location','site','sitio','planta','sede'],
  creado:    ['creado','created','fecha creación','fecha creacion','creation date','fecha_creacion','date created'],
  Resuelto:  ['resuelto','resolved','fecha resolución','fecha resolucion','resolution date','fecha_resolucion','date resolved'],
  Detalle:   ['detalle','detail','descripción','descripcion','description','summary','resumen','asunto','subject'],
  'Causa de cierre': ['causa de cierre','causa_de_cierre','causa cierre','close reason','razon cierre','razón cierre','resolution','motivo cierre'],
  'Fecha vencimiento': ['fecha vencimiento','fecha_vencimiento','due date','vencimiento','fecha límite','fecha limite','due_date','sla date']
}

function buildKeyMap(headers) {
  const map = {}
  for (const [standard, aliases] of Object.entries(FIELD_ALIASES)) {
    for (const h of headers) {
      const normalized = h.trim().toLowerCase().replace(/[_\-]+/g, ' ')
      if (aliases.includes(normalized) || normalized === standard.toLowerCase()) {
        map[standard] = h
        break
      }
    }
  }
  return map
}

function getField(row, fieldName) {
  if (!row) return '—'
  // Direct access
  if (row[fieldName] != null && row[fieldName] !== '') return String(row[fieldName]).trim()
  // Via key map
  const mapped = columnKeys.value[fieldName]
  if (mapped && row[mapped] != null && row[mapped] !== '') return String(row[mapped]).trim()
  // Case-insensitive fallback
  const lower = fieldName.toLowerCase()
  for (const k of Object.keys(row)) {
    if (k.toLowerCase().trim() === lower || k.toLowerCase().trim().replace(/[_\-]+/g, ' ') === lower.replace(/[_\-]+/g, ' ')) {
      if (row[k] != null && row[k] !== '') return String(row[k]).trim()
    }
  }
  return '—'
}

/* ================================================================
   CSV PARSING
   ================================================================ */
function parseCSV(file) {
  return new Promise((resolve, reject) => {
    Papa.parse(file, {
      header: true,
      skipEmptyLines: true,
      transformHeader: h => h.trim(),
      complete: res => resolve(res),
      error: err => reject(err)
    })
  })
}

async function loadData(file) {
  try {
    const result = await parseCSV(file)
    const headers = result.meta.fields || []
    columnKeys.value = buildKeyMap(headers)

    reportData.value = result.data.map(row => {
      const creado = getField(row, 'creado')
      if (creado && creado !== '—') {
        try {
          const d = parse(creado, 'MMM d, yyyy', new Date(), { locale: enUS })
          row._dayOfWeek = format(d, 'EEEE', { locale: enUS })
        } catch {
          try {
            const d = new Date(creado)
            if (!isNaN(d)) row._dayOfWeek = format(d, 'EEEE', { locale: enUS })
          } catch { row._dayOfWeek = 'N/A' }
        }
      }
      return row
    })
    currentPage.value = 1
  } catch (e) {
    alert('Error al leer el archivo CSV')
    console.error(e)
  }
}

function handleFileInput(e) { const f = e.target.files[0]; if (f) loadData(f) }
function handleDrop(e) { dragging.value = false; const f = e.dataTransfer.files[0]; if (f) loadData(f) }
function resetData() { reportData.value = []; tableSearch.value = ''; sortKey.value = ''; currentPage.value = 1 }

/* ================================================================
   COMPUTED METRICS
   ================================================================ */
const dateRange = computed(() => {
  if (!hasData.value) return ''
  const dates = reportData.value.map(t => {
    const v = getField(t, 'creado')
    if (v === '—') return null
    try { return parse(v, 'MMM d, yyyy', new Date(), { locale: enUS }) }
    catch { try { const d = new Date(v); return isNaN(d) ? null : d } catch { return null } }
  }).filter(Boolean).sort((a, b) => a - b)
  if (!dates.length) return 'Sin fechas'
  return `${format(dates[0], 'dd MMM yyyy')} — ${format(dates.at(-1), 'dd MMM yyyy')}`
})

const metrics = computed(() => {
  const d = reportData.value
  if (!d.length) return { total: 0, open: 0, resolvedCount: 0, avgTTR: '0', sla: '0', criticals: 0, slaRisk: 0, topics: [], mostCriticalSystem: 'N/A' }

  const resolved = d.filter(t => {
    const s = getField(t, 'Status').toUpperCase()
    return ['CLOSED', 'RESOLVED', 'CERRADO', 'RESUELTO', 'DONE', 'COMPLETADO'].includes(s)
  })

  let totalDays = 0
  let validDays = 0
  resolved.forEach(t => {
    try {
      const c = parse(getField(t, 'creado'), 'MMM d, yyyy', new Date(), { locale: enUS })
      const r = parse(getField(t, 'Resuelto'), 'MMM d, yyyy', new Date(), { locale: enUS })
      totalDays += Math.abs(differenceInDays(r, c))
      validDays++
    } catch {}
  })

  const criticals = d.filter(t => {
    const p = getField(t, 'Prioridad').toLowerCase()
    return (p.includes('1') || p.includes('critical') || p.includes('crítica') || p.includes('critica') || p.includes('highest'))
      && !resolved.includes(t)
  })

  const words = d.map(t => getField(t, 'Detalle')).join(' ').split(/\W+/).filter(w => w.length > 5)
  const wordFreq = {}
  words.forEach(w => { const lw = w.toLowerCase(); wordFreq[lw] = (wordFreq[lw] || 0) + 1 })
  const topWords = Object.entries(wordFreq).sort((a, b) => b[1] - a[1]).slice(0, 4).map(e => e[0])

  return {
    total: d.length,
    open: d.length - resolved.length,
    resolvedCount: resolved.length,
    avgTTR: validDays ? (totalDays / validDays).toFixed(1) : '0',
    sla: ((resolved.length / d.length) * 100).toFixed(0),
    criticals: criticals.length,
    slaRisk: criticals.length,
    topics: topWords,
    mostCriticalSystem: criticals.length ? getField(criticals[0], 'Sistema') : 'N/A'
  }
})

/* ================================================================
   KPI CARDS
   ================================================================ */
const kpiCards = computed(() => {
  const m = metrics.value
  if (!m.total) return []
  return [
    { label: 'Total Tickets', value: m.total, color: '#818cf8', bg: 'rgba(129,140,248,0.08)', pct: 100, sub: 'registros cargados',
      svg: '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2"/><rect x="8" y="2" width="8" height="4" rx="1" ry="1"/></svg>' },
    { label: 'Abiertos', value: m.open, color: '#fb923c', bg: 'rgba(251,146,60,0.08)', pct: Math.round(m.open / m.total * 100), sub: `${Math.round(m.open / m.total * 100)}% del total`,
      svg: '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>' },
    { label: 'Resueltos', value: m.resolvedCount, color: '#34d399', bg: 'rgba(52,211,153,0.08)', pct: Math.round(m.resolvedCount / m.total * 100), sub: `${Math.round(m.resolvedCount / m.total * 100)}% completados`,
      svg: '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><polyline points="22 4 12 14.01 9 11.01"/></svg>' },
    { label: 'TTR Promedio', value: m.avgTTR + ' días', color: '#22d3ee', bg: 'rgba(34,211,238,0.08)', pct: Math.min(100, Math.round(m.avgTTR * 8)), sub: 'tiempo resolución',
      svg: '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12"/><path d="M12 12 15 15"/></svg>' },
    { label: 'Cumplimiento SLA', value: m.sla + '%', color: '#60a5fa', bg: 'rgba(96,165,250,0.08)', pct: Number(m.sla), sub: 'nivel de servicio',
      svg: '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/></svg>' },
    { label: 'Críticos Abiertos', value: m.criticals, color: '#f87171', bg: 'rgba(248,113,113,0.08)', pct: m.total ? Math.round(m.criticals / m.total * 100) : 0, sub: 'requieren atención',
      svg: '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>' }
  ]
})

/* ================================================================
   CHART HELPERS
   ================================================================ */
const COLORS = [
  '#06b6d4','#8b5cf6','#f59e0b','#ec4899','#10b981',
  '#3b82f6','#ef4444','#6366f1','#14b8a6','#f97316',
  '#a855f7','#84cc16','#0ea5e9','#d946ef','#22d3ee','#fb923c'
]

const STATUS_COLORS = {
  open: '#fb923c', abierto: '#fb923c',
  'in progress': '#3b82f6', 'en progreso': '#3b82f6', 'in_progress': '#3b82f6',
  resolved: '#10b981', resuelto: '#10b981',
  closed: '#06b6d4', cerrado: '#06b6d4',
  done: '#10b981', completado: '#10b981',
  waiting: '#a855f7', esperando: '#a855f7',
  pending: '#f59e0b', pendiente: '#f59e0b',
  cancelled: '#64748b', cancelado: '#64748b'
}

const PRIO_COLORS = {
  '1': '#ef4444', 'highest': '#ef4444', 'critical': '#ef4444', 'crítica': '#ef4444',
  '2': '#f97316', 'high': '#f97316', 'alta': '#f97316',
  '3': '#eab308', 'medium': '#eab308', 'media': '#eab308',
  '4': '#22c55e', 'low': '#22c55e', 'baja': '#22c55e',
  '5': '#94a3b8', 'lowest': '#94a3b8', 'muy baja': '#94a3b8'
}

function getStatusColor(status) {
  const s = (status || '').toLowerCase().trim()
  for (const [k, v] of Object.entries(STATUS_COLORS)) {
    if (s.includes(k)) return v
  }
  return '#64748b'
}

function getPrioColor(prio) {
  const p = (prio || '').toLowerCase().trim()
  for (const [k, v] of Object.entries(PRIO_COLORS)) {
    if (p.includes(k)) return v
  }
  return '#64748b'
}

function groupByField(field, limit) {
  const counts = {}
  reportData.value.forEach(r => {
    const k = field === '_dayOfWeek' ? (r._dayOfWeek || 'N/A') : getField(r, field)
    counts[k] = (counts[k] || 0) + 1
  })
  let entries = Object.entries(counts).filter(e => e[0] !== '—').sort((a, b) => b[1] - a[1])
  if (limit) entries = entries.slice(0, limit)
  return { labels: entries.map(e => e[0]), values: entries.map(e => e[1]) }
}

// ── Chart options ──
const tooltipStyle = {
  backgroundColor: 'rgba(15,23,42,0.95)', titleColor: '#f1f5f9', bodyColor: '#94a3b8',
  borderColor: 'rgba(6,182,212,0.2)', borderWidth: 1, cornerRadius: 10,
  padding: 12, titleFont: { weight: 'bold', size: 12 }, bodyFont: { size: 11 },
  displayColors: true, boxPadding: 4
}

const barOpts = {
  responsive: true, maintainAspectRatio: false,
  plugins: { legend: { display: false }, tooltip: tooltipStyle },
  scales: {
    x: { ticks: { color: '#64748b', font: { size: 9, weight: '500' } }, grid: { display: false }, border: { display: false } },
    y: { ticks: { color: '#475569', font: { size: 9 } }, grid: { color: 'rgba(255,255,255,0.03)', drawBorder: false }, border: { display: false }, beginAtZero: true }
  }
}
const barHOpts = { ...barOpts, indexAxis: 'y',
  scales: {
    y: { ticks: { color: '#94a3b8', font: { size: 10, weight: '500' } }, grid: { display: false }, border: { display: false } },
    x: { ticks: { color: '#475569', font: { size: 9 } }, grid: { color: 'rgba(255,255,255,0.03)' }, border: { display: false }, beginAtZero: true }
  }
}
const lineOpts = {
  responsive: true, maintainAspectRatio: false,
  plugins: { legend: { display: false }, tooltip: tooltipStyle },
  scales: {
    x: { ticks: { color: '#64748b', font: { size: 9 }, maxRotation: 45 }, grid: { display: false }, border: { display: false } },
    y: { ticks: { color: '#475569', font: { size: 9 } }, grid: { color: 'rgba(6,182,212,0.04)' }, border: { display: false }, beginAtZero: true }
  },
  elements: { point: { radius: 3, hoverRadius: 6, borderWidth: 2 } }
}
const doughnutOpts = {
  responsive: true, maintainAspectRatio: false, cutout: '65%',
  plugins: {
    legend: { position: 'bottom', labels: { color: '#94a3b8', font: { size: 10, weight: '500' }, padding: 14, usePointStyle: true, pointStyleWidth: 8 } },
    tooltip: tooltipStyle
  }
}
const polarOpts = {
  responsive: true, maintainAspectRatio: false,
  plugins: {
    legend: { position: 'bottom', labels: { color: '#94a3b8', font: { size: 10, weight: '500' }, padding: 14, usePointStyle: true, pointStyleWidth: 8 } },
    tooltip: tooltipStyle
  },
  scales: { r: { ticks: { display: false }, grid: { color: 'rgba(255,255,255,0.04)' }, pointLabels: { display: false } } }
}

// ── Chart data ──
const chartVencimientos = computed(() => {
  const g = groupByField('Fecha vencimiento')
  return {
    labels: g.labels,
    datasets: [{
      label: 'Tickets', data: g.values,
      borderColor: '#06b6d4', backgroundColor: 'rgba(6,182,212,0.06)',
      fill: true, tension: 0.4, pointBackgroundColor: '#06b6d4',
      pointBorderColor: '#0b1120', pointBorderWidth: 2, borderWidth: 2
    }]
  }
})
const chartSemanal = computed(() => {
  const g = groupByField('_dayOfWeek')
  return {
    labels: g.labels,
    datasets: [{
      label: 'Tickets', data: g.values,
      backgroundColor: g.labels.map((_, i) => COLORS[i % COLORS.length] + 'bb'),
      borderColor: g.labels.map((_, i) => COLORS[i % COLORS.length]),
      borderWidth: 1, borderRadius: 8, borderSkipped: false
    }]
  }
})
const chartDemanda = computed(() => {
  const g = groupByField('issuetype')
  return {
    labels: g.labels,
    datasets: [{
      data: g.values,
      backgroundColor: g.labels.map((_, i) => COLORS[i % COLORS.length] + '99'),
      borderColor: g.labels.map((_, i) => COLORS[i % COLORS.length]),
      borderWidth: 2
    }]
  }
})
const chartCierre = computed(() => {
  const g = groupByField('Causa de cierre')
  return {
    labels: g.labels,
    datasets: [{
      label: 'Tickets', data: g.values,
      backgroundColor: g.labels.map((_, i) => COLORS[i % COLORS.length] + 'bb'),
      borderColor: g.labels.map((_, i) => COLORS[i % COLORS.length]),
      borderWidth: 1, borderRadius: 6
    }]
  }
})
const chartEstatus = computed(() => {
  const g = groupByField('Status')
  return {
    labels: g.labels,
    datasets: [{
      data: g.values,
      backgroundColor: g.labels.map(l => getStatusColor(l) + 'cc'),
      borderColor: g.labels.map(l => getStatusColor(l)),
      borderWidth: 2, hoverOffset: 6
    }]
  }
})
const chartPrioridad = computed(() => {
  const g = groupByField('Prioridad')
  return {
    labels: g.labels,
    datasets: [{
      data: g.values,
      backgroundColor: g.labels.map(l => getPrioColor(l) + 'cc'),
      borderColor: g.labels.map(l => getPrioColor(l)),
      borderWidth: 2, hoverOffset: 6
    }]
  }
})
const chartUbicacion = computed(() => {
  const g = groupByField('Ubicacion')
  return {
    labels: g.labels,
    datasets: [{
      data: g.values,
      backgroundColor: g.labels.map((_, i) => COLORS[(i + 4) % COLORS.length] + '99'),
      borderColor: g.labels.map((_, i) => COLORS[(i + 4) % COLORS.length]),
      borderWidth: 2
    }]
  }
})
const chartSistema = computed(() => {
  const g = groupByField('Sistema')
  return {
    labels: g.labels,
    datasets: [{
      label: 'Tickets', data: g.values,
      backgroundColor: g.labels.map((_, i) => COLORS[(i + 2) % COLORS.length] + 'bb'),
      borderColor: g.labels.map((_, i) => COLORS[(i + 2) % COLORS.length]),
      borderWidth: 1, borderRadius: 8, borderSkipped: false
    }]
  }
})
const chartAgentes = computed(() => {
  const g = groupByField('Agente', 10)
  return {
    labels: g.labels,
    datasets: [{
      label: 'Tickets', data: g.values,
      backgroundColor: g.labels.map((_, i) => COLORS[i % COLORS.length] + 'bb'),
      borderColor: g.labels.map((_, i) => COLORS[i % COLORS.length]),
      borderWidth: 1, borderRadius: 6
    }]
  }
})

/* ================================================================
   TABLE LOGIC
   ================================================================ */
const tableCols = [
  { key: 'Key', label: 'Ticket' },
  { key: 'Status', label: 'Estado' },
  { key: 'Prioridad', label: 'Prioridad' },
  { key: 'issuetype', label: 'Tipo' },
  { key: 'Sistema', label: 'Sistema' },
  { key: 'Agente', label: 'Agente' },
  { key: 'Ubicacion', label: 'Ubicación' },
  { key: 'creado', label: 'Creado' },
  { key: 'Resuelto', label: 'Resuelto' }
]

const filteredTickets = computed(() => {
  let list = [...reportData.value]
  if (tableSearch.value) {
    const q = tableSearch.value.toLowerCase()
    list = list.filter(t =>
      tableCols.some(c => getField(t, c.key).toLowerCase().includes(q))
    )
  }
  if (sortKey.value) {
    list.sort((a, b) => {
      const va = getField(a, sortKey.value).toLowerCase()
      const vb = getField(b, sortKey.value).toLowerCase()
      return sortDir.value === 'asc' ? va.localeCompare(vb) : vb.localeCompare(va)
    })
  }
  return list
})

const totalPages = computed(() => Math.max(1, Math.ceil(filteredTickets.value.length / ROWS_PER_PAGE)))
const paginatedTickets = computed(() => {
  const s = (currentPage.value - 1) * ROWS_PER_PAGE
  return filteredTickets.value.slice(s, s + ROWS_PER_PAGE)
})
const visiblePages = computed(() => {
  const pages = []
  const start = Math.max(1, currentPage.value - 2)
  const end = Math.min(totalPages.value, start + 4)
  for (let i = start; i <= end; i++) pages.push(i)
  return pages
})

watch(tableSearch, () => { currentPage.value = 1 })

function sortTable(key) {
  if (sortKey.value === key) sortDir.value = sortDir.value === 'asc' ? 'desc' : 'asc'
  else { sortKey.value = key; sortDir.value = 'asc' }
}

function statusChip(s) {
  const u = (s || '').toLowerCase()
  if (u.includes('closed') || u.includes('cerrado')) return 'chip-teal'
  if (u.includes('resolved') || u.includes('resuelto') || u.includes('done')) return 'chip-green'
  if (u.includes('progress') || u.includes('progreso')) return 'chip-blue'
  if (u.includes('open') || u.includes('abierto')) return 'chip-amber'
  if (u.includes('waiting') || u.includes('pending') || u.includes('esperando')) return 'chip-violet'
  return 'chip-slate'
}
function prioChip(p) {
  const u = (p || '').toLowerCase()
  if (u.includes('1') || u.includes('critical') || u.includes('highest')) return 'chip-red'
  if (u.includes('2') || u.includes('high') || u.includes('alta')) return 'chip-orange'
  if (u.includes('3') || u.includes('medium') || u.includes('media')) return 'chip-yellow'
  if (u.includes('4') || u.includes('low') || u.includes('baja')) return 'chip-green'
  return 'chip-slate'
}
function rowClass(t) {
  const s = getField(t, 'Status').toLowerCase()
  if (s.includes('open') || s.includes('abierto')) return 'row-open'
  return ''
}

/* ================================================================
   PDF EXPORT
   ================================================================ */
async function exportFullPDF() {
  if (isExporting.value) return
  isExporting.value = true
  try {
    const pdf = new jsPDF({ orientation: 'landscape', unit: 'mm', format: 'a4' })
    const W = 297, H = 210, M = 14

    // ── COVER ──
    drawCoverPage(pdf, W, H)

    // ── EXECUTIVE SUMMARY ──
    pdf.addPage()
    drawExecutiveSummary(pdf, W, H)

    // ── CHART SECTIONS ──
    const sections = [secKPI, secRisk, secCharts1, secCharts2, secCharts3, secAgentes]
    for (const s of sections) {
      if (!s.value) continue
      pdf.addPage()
      drawPageBg(pdf, W, H)
      try {
        const imgData = await toJpeg(s.value, { pixelRatio: 2.5, backgroundColor: '#080d1a', quality: 0.92 })
        const img = new Image(); img.src = imgData
        await new Promise(r => { img.onload = r })
        const usable = W - M * 2
        let imgH = (img.height * usable) / img.width
        let imgW = usable
        if (imgH > H - 44) { imgH = H - 44; imgW = (img.width * imgH) / img.height }
        const x = (W - imgW) / 2
        pdf.addImage(imgData, 'JPEG', x, 22, imgW, imgH)
      } catch (e) { console.warn('Section capture failed:', e) }
      drawPageFooter(pdf, W, H)
    }

    // ── TABLE PAGES ──
    drawTablePages(pdf, W, H)

    pdf.save(`Reporte_Ejecutivo_DEACERO_${format(new Date(), 'yyyy-MM-dd_HHmm')}.pdf`)
  } catch (e) {
    console.error(e)
    alert('Error al generar PDF')
  } finally {
    isExporting.value = false
  }
}

function drawCoverPage(pdf, W, H) {
  // Dark background
  pdf.setFillColor(8, 13, 26)
  pdf.rect(0, 0, W, H, 'F')

  // Geometric accent shapes
  pdf.setFillColor(6, 182, 212)
  pdf.rect(0, 0, 5, H, 'F')
  pdf.setFillColor(99, 102, 241)
  pdf.triangle(W - 80, 0, W, 0, W, 80, 'F')
  pdf.setFillColor(139, 92, 246)
  pdf.setGlobalAlpha && pdf.setGState && pdf.setGState(new pdf.GState({ opacity: 0.3 }))

  // Reset opacity fallback
  pdf.setFillColor(6, 182, 212)
  pdf.rect(0, H - 3, W, 3, 'F')

  // Title
  pdf.setTextColor(255, 255, 255)
  pdf.setFont('helvetica', 'bold')
  pdf.setFontSize(42)
  pdf.text('DEACERO', 28, 65)
  pdf.setTextColor(6, 182, 212)
  pdf.text('ANALYTICS', 28, 82)

  // Subtitle
  pdf.setFontSize(13)
  pdf.setTextColor(148, 163, 184)
  pdf.setFont('helvetica', 'normal')
  pdf.text('Reporte Ejecutivo de Operatividad Digital', 28, 100)

  // Separator line
  pdf.setDrawColor(6, 182, 212)
  pdf.setLineWidth(0.5)
  pdf.line(28, 108, 160, 108)

  // Info block
  pdf.setFontSize(10)
  pdf.setTextColor(100, 116, 139)
  pdf.text(`Período: ${dateRange.value}`, 28, 120)
  pdf.text(`Generado: ${format(new Date(), "dd 'de' MMMM yyyy — HH:mm")}`, 28, 128)
  pdf.text(`Total de registros: ${reportData.value.length} tickets`, 28, 136)

  // KPI Summary Box
  const m = metrics.value
  const boxY = 152, boxH = 36
  pdf.setFillColor(12, 18, 35)
  pdf.roundedRect(24, boxY, W - 48, boxH, 6, 6, 'F')
  pdf.setDrawColor(30, 41, 59)
  pdf.setLineWidth(0.3)
  pdf.roundedRect(24, boxY, W - 48, boxH, 6, 6, 'S')

  const kpis = [
    { l: 'Abiertos', v: String(m.open), c: [251, 146, 60] },
    { l: 'Resueltos', v: String(m.resolvedCount), c: [52, 211, 153] },
    { l: 'TTR Prom.', v: m.avgTTR + ' d', c: [34, 211, 238] },
    { l: 'SLA', v: m.sla + '%', c: [96, 165, 250] },
    { l: 'Críticos', v: String(m.criticals), c: [248, 113, 113] }
  ]
  const kpiW = (W - 48) / kpis.length
  kpis.forEach((k, i) => {
    const cx = 24 + kpiW * i + kpiW / 2
    pdf.setFontSize(7)
    pdf.setTextColor(100, 116, 139)
    pdf.text(k.l, cx, boxY + 12, { align: 'center' })
    pdf.setFontSize(18)
    pdf.setFont('helvetica', 'bold')
    pdf.setTextColor(...k.c)
    pdf.text(k.v, cx, boxY + 26, { align: 'center' })
    pdf.setFont('helvetica', 'normal')
    // Divider
    if (i < kpis.length - 1) {
      pdf.setDrawColor(30, 41, 59)
      pdf.setLineWidth(0.2)
      pdf.line(24 + kpiW * (i + 1), boxY + 6, 24 + kpiW * (i + 1), boxY + boxH - 6)
    }
  })
}

function drawExecutiveSummary(pdf, W, H) {
  drawPageBg(pdf, W, H)
  const m = metrics.value

  // Title
  pdf.setFont('helvetica', 'bold')
  pdf.setFontSize(16)
  pdf.setTextColor(241, 245, 249)
  pdf.text('Resumen Ejecutivo', 20, 24)
  pdf.setDrawColor(6, 182, 212)
  pdf.setLineWidth(0.8)
  pdf.line(20, 28, 100, 28)

  // KPI Detail Cards
  const cards = [
    { title: 'Total Tickets', value: String(m.total), desc: 'Registros procesados', c: [129, 140, 248] },
    { title: 'Tickets Abiertos', value: String(m.open), desc: `${Math.round(m.open / m.total * 100)}% del total`, c: [251, 146, 60] },
    { title: 'Tickets Resueltos', value: String(m.resolvedCount), desc: `${Math.round(m.resolvedCount / m.total * 100)}% completados`, c: [52, 211, 153] },
    { title: 'TTR Promedio', value: m.avgTTR + ' días', desc: 'Tiempo de resolución', c: [34, 211, 238] },
    { title: 'Cumplimiento SLA', value: m.sla + '%', desc: 'Nivel de servicio', c: [96, 165, 250] },
    { title: 'Prioridad Crítica', value: String(m.criticals), desc: 'Requieren atención inmediata', c: [248, 113, 113] }
  ]

  const cardW = (W - 60) / 3, cardH = 52, gap = 10, startX = 20, startY = 40
  cards.forEach((card, i) => {
    const col = i % 3, row = Math.floor(i / 3)
    const x = startX + col * (cardW + gap)
    const y = startY + row * (cardH + gap)

    // Card bg
    pdf.setFillColor(12, 18, 35)
    pdf.roundedRect(x, y, cardW, cardH, 5, 5, 'F')
    pdf.setDrawColor(20, 30, 50)
    pdf.setLineWidth(0.3)
    pdf.roundedRect(x, y, cardW, cardH, 5, 5, 'S')

    // Accent top
    pdf.setFillColor(...card.c)
    pdf.rect(x, y, cardW, 2, 'F')

    // Title
    pdf.setFontSize(8)
    pdf.setTextColor(100, 116, 139)
    pdf.setFont('helvetica', 'normal')
    pdf.text(card.title.toUpperCase(), x + 12, y + 16)

    // Value
    pdf.setFontSize(22)
    pdf.setFont('helvetica', 'bold')
    pdf.setTextColor(...card.c)
    pdf.text(card.value, x + 12, y + 34)

    // Description
    pdf.setFontSize(7)
    pdf.setFont('helvetica', 'normal')
    pdf.setTextColor(71, 85, 105)
    pdf.text(card.desc, x + 12, y + 44)
  })

  // System info
  const infoY = startY + 2 * (cardH + gap) + 20
  pdf.setFillColor(12, 18, 35)
  pdf.roundedRect(20, infoY, W - 40, 30, 5, 5, 'F')
  pdf.setDrawColor(20, 30, 50)
  pdf.roundedRect(20, infoY, W - 40, 30, 5, 5, 'S')
  pdf.setFontSize(8)
  pdf.setTextColor(100, 116, 139)
  pdf.text('Sistema Más Crítico:', 30, infoY + 12)
  pdf.setFontSize(12)
  pdf.setFont('helvetica', 'bold')
  pdf.setTextColor(251, 191, 36)
  pdf.text(m.mostCriticalSystem, 30, infoY + 23)

  if (m.topics?.length) {
    pdf.setFontSize(8)
    pdf.setFont('helvetica', 'normal')
    pdf.setTextColor(100, 116, 139)
    pdf.text('Tópicos Frecuentes:', 150, infoY + 12)
    pdf.setFontSize(10)
    pdf.setFont('helvetica', 'bold')
    pdf.setTextColor(165, 180, 252)
    pdf.text(m.topics.join('  •  '), 150, infoY + 23)
  }

  drawPageFooter(pdf, W, H)
}

function drawPageBg(pdf, W, H) {
  pdf.setFillColor(8, 13, 26)
  pdf.rect(0, 0, W, H, 'F')
  // Top accent
  pdf.setFillColor(6, 182, 212)
  pdf.rect(0, 0, W, 1.5, 'F')
  // Left accent
  pdf.setFillColor(99, 102, 241)
  pdf.rect(0, 0, 2, H, 'F')
}

function drawPageFooter(pdf, W, H) {
  pdf.setFillColor(5, 8, 18)
  pdf.rect(0, H - 10, W, 10, 'F')
  pdf.setDrawColor(20, 30, 50)
  pdf.setLineWidth(0.2)
  pdf.line(0, H - 10, W, H - 10)

  pdf.setFont('helvetica', 'normal')
  pdf.setFontSize(6.5)
  pdf.setTextColor(71, 85, 105)
  pdf.text('DEACERO ANALYTICS  —  Reporte Ejecutivo Confidencial', 12, H - 4)

  const pn = pdf.getNumberOfPages()
  pdf.setTextColor(100, 116, 139)
  pdf.text(`Página ${pn}`, W - 30, H - 4)

  // Date
  pdf.setTextColor(50, 60, 80)
  pdf.text(format(new Date(), 'dd/MM/yyyy HH:mm'), W / 2 - 10, H - 4)
}

function drawTablePages(pdf, W, H) {
  const data = reportData.value
  if (!data.length) return

  const headers = ['Ticket', 'Estado', 'Prioridad', 'Tipo', 'Sistema', 'Agente', 'Ubicación', 'Creado', 'Resuelto']
  const keys = ['Key', 'Status', 'Prioridad', 'issuetype', 'Sistema', 'Agente', 'Ubicacion', 'creado', 'Resuelto']
  const colWidths = [26, 24, 22, 26, 32, 36, 28, 26, 26]
  const tableW = colWidths.reduce((a, b) => a + b, 0)
  const startX = (W - tableW) / 2
  const rowH = 7.5
  const headerY = 34
  const maxPerPage = Math.floor((H - 56) / rowH)

  for (let page = 0; page * maxPerPage < data.length; page++) {
    pdf.addPage()
    drawPageBg(pdf, W, H)

    // Section title
    pdf.setFont('helvetica', 'bold')
    pdf.setFontSize(13)
    pdf.setTextColor(241, 245, 249)
    pdf.text('Detalle de Tickets', startX, 18)
    pdf.setFontSize(8)
    pdf.setTextColor(71, 85, 105)
    pdf.setFont('helvetica', 'normal')
    pdf.text(`Mostrando ${page * maxPerPage + 1}–${Math.min((page + 1) * maxPerPage, data.length)} de ${data.length}`, startX, 26)

    // Table header background
    pdf.setFillColor(13, 20, 38)
    pdf.roundedRect(startX - 2, headerY - 6, tableW + 4, rowH + 2, 2, 2, 'F')
    pdf.setDrawColor(6, 182, 212)
    pdf.setLineWidth(0.3)
    pdf.line(startX - 2, headerY + rowH - 4, startX + tableW + 2, headerY + rowH - 4)

    // Header text
    let x = startX
    pdf.setFont('helvetica', 'bold')
    pdf.setFontSize(6.5)
    pdf.setTextColor(6, 182, 212)
    headers.forEach((h, i) => {
      pdf.text(h.toUpperCase(), x + 2, headerY + 1)
      x += colWidths[i]
    })

    // Rows
    const slice = data.slice(page * maxPerPage, (page + 1) * maxPerPage)
    slice.forEach((row, ri) => {
      const y = headerY + (ri + 1) * rowH
      // Alternating row bg
      if (ri % 2 === 0) {
        pdf.setFillColor(10, 15, 28)
        pdf.rect(startX - 2, y - 5, tableW + 4, rowH, 'F')
      }

      x = startX
      keys.forEach((k, ci) => {
        const val = getField(row, k)
        const maxChars = Math.floor(colWidths[ci] / 1.6)
        const displayVal = val.length > maxChars ? val.substring(0, maxChars - 1) + '…' : val

        // Status column color coding
        if (k === 'Status') {
          const sc = getStatusColor(val)
          const rgb = hexToRgb(sc)
          pdf.setFillColor(rgb.r, rgb.g, rgb.b)
          pdf.roundedRect(x + 1, y - 4, colWidths[ci] - 3, 5.5, 1.5, 1.5, 'F')
          pdf.setFont('helvetica', 'bold')
          pdf.setFontSize(5.5)
          pdf.setTextColor(255, 255, 255)
          pdf.text(displayVal.toUpperCase(), x + 3, y)
          pdf.setFont('helvetica', 'normal')
          pdf.setFontSize(6)
        }
        // Priority column color coding
        else if (k === 'Prioridad') {
          const pc = getPrioColor(val)
          const rgb = hexToRgb(pc)
          pdf.setFillColor(rgb.r, rgb.g, rgb.b)
          pdf.roundedRect(x + 1, y - 4, colWidths[ci] - 3, 5.5, 1.5, 1.5, 'F')
          pdf.setFont('helvetica', 'bold')
          pdf.setFontSize(5.5)
          pdf.setTextColor(255, 255, 255)
          pdf.text(displayVal.toUpperCase(), x + 3, y)
          pdf.setFont('helvetica', 'normal')
          pdf.setFontSize(6)
        }
        // Ticket key styling
        else if (k === 'Key') {
          pdf.setFont('helvetica', 'bold')
          pdf.setFontSize(6)
          pdf.setTextColor(6, 182, 212)
          pdf.text(displayVal, x + 2, y)
          pdf.setFont('helvetica', 'normal')
        }
        else {
          pdf.setFont('helvetica', 'normal')
          pdf.setFontSize(6)
          pdf.setTextColor(203, 213, 225)
          pdf.text(displayVal, x + 2, y)
        }

        x += colWidths[ci]
      })

      // Row bottom line
      pdf.setDrawColor(20, 30, 50)
      pdf.setLineWidth(0.1)
      pdf.line(startX - 2, y + 2.5, startX + tableW + 2, y + 2.5)
    })

    // Table border
    const tableH = (slice.length + 1) * rowH + 4
    pdf.setDrawColor(30, 41, 59)
    pdf.setLineWidth(0.3)
    pdf.roundedRect(startX - 2, headerY - 6, tableW + 4, tableH, 3, 3, 'S')

    drawPageFooter(pdf, W, H)
  }
}

function hexToRgb(hex) {
  const h = hex.replace('#', '')
  return {
    r: parseInt(h.substring(0, 2), 16),
    g: parseInt(h.substring(2, 4), 16),
    b: parseInt(h.substring(4, 6), 16)
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=JetBrains+Mono:wght@400;600&display=swap');

*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
.sr-only{position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0,0,0,0);border:0}

.app-root{
  font-family:'Inter',system-ui,-apple-system,sans-serif;
  min-height:100vh;
  background:#060a14;
  color:#e2e8f0;
  -webkit-font-smoothing:antialiased;
  position:relative;
  overflow-x:hidden;
}
.bg-grid{
  position:fixed;inset:0;z-index:0;pointer-events:none;
  background-image:
    radial-gradient(circle at 20% 30%, rgba(6,182,212,0.03) 0%, transparent 50%),
    radial-gradient(circle at 80% 70%, rgba(99,102,241,0.03) 0%, transparent 50%),
    linear-gradient(rgba(255,255,255,0.01) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255,255,255,0.01) 1px, transparent 1px);
  background-size:100% 100%, 100% 100%, 40px 40px, 40px 40px;
}

/* ─── SCROLLBAR ─── */
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:#1e293b;border-radius:99px}
::-webkit-scrollbar-thumb:hover{background:#334155}

/* ─── NAV ─── */
.main-nav{
  position:sticky;top:0;z-index:100;
  background:rgba(6,10,20,0.8);
  backdrop-filter:blur(20px) saturate(1.6);
  border-bottom:1px solid rgba(255,255,255,0.04);
  padding:0 1.5rem;
}
.nav-inner{max-width:1600px;margin:0 auto;display:flex;align-items:center;justify-content:space-between;height:56px}
.nav-glow{position:absolute;bottom:-1px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,rgba(6,182,212,0.3),rgba(99,102,241,0.3),transparent)}
.nav-brand{display:flex;align-items:center;gap:12px}
.logo-box{
  width:36px;height:36px;border-radius:10px;
  background:linear-gradient(135deg,#06b6d4,#6366f1);
  display:flex;align-items:center;justify-content:center;
  box-shadow:0 0 16px rgba(6,182,212,0.2);
}
.logo-svg{width:18px;height:18px;color:#fff}
.brand-title{font-size:14px;font-weight:900;letter-spacing:0.08em;color:#f1f5f9}
.brand-title span{margin-left:4px;background:linear-gradient(90deg,#06b6d4,#818cf8);-webkit-background-clip:text;-webkit-text-fill-color:transparent}
.brand-sub{font-size:8px;font-weight:600;color:#475569;letter-spacing:0.25em;text-transform:uppercase;display:flex;align-items:center;gap:6px;margin-top:1px}
.pulse-dot{width:5px;height:5px;border-radius:50%;background:#06b6d4;animation:pulse 2s infinite;display:inline-block}
@keyframes pulse{0%,100%{opacity:1;box-shadow:0 0 0 0 rgba(6,182,212,0.4)}50%{opacity:.7;box-shadow:0 0 0 4px rgba(6,182,212,0)}}

.nav-actions{display:flex;align-items:center;gap:10px}
.nav-stats{display:flex;align-items:center;gap:8px;font-size:11px;color:#64748b;font-weight:600;margin-right:8px}
.nav-stats strong{color:#e2e8f0}
.nav-divider{color:#1e293b}
.live-indicator{color:#10b981;font-size:10px}

.btn-ghost{
  padding:7px 14px;border-radius:10px;font-size:11px;font-weight:600;
  color:#94a3b8;border:1px solid rgba(255,255,255,0.06);background:transparent;
  cursor:pointer;transition:all .2s;display:flex;align-items:center;gap:5px;
}
.btn-ghost:hover{color:#e2e8f0;border-color:rgba(255,255,255,0.12);background:rgba(255,255,255,0.03)}
.btn-export{
  padding:8px 18px;border-radius:10px;font-size:11px;font-weight:700;
  color:#fff;border:none;cursor:pointer;transition:all .25s;
  background:linear-gradient(135deg,#0891b2,#6366f1);
  box-shadow:0 4px 20px rgba(6,182,212,0.2);
  display:flex;align-items:center;gap:6px;
}
.btn-export:hover{transform:translateY(-1px);box-shadow:0 6px 28px rgba(6,182,212,0.3)}
.btn-export:active{transform:scale(.97)}
.btn-export:disabled{opacity:.5;cursor:not-allowed;transform:none}

/* ─── UPLOAD ─── */
.upload-screen{display:flex;align-items:center;justify-content:center;min-height:calc(100vh - 56px);padding:2rem;position:relative;z-index:1}
.upload-card{max-width:520px;width:100%;text-align:center;padding:40px 32px;background:rgba(255,255,255,0.015);border:1px solid rgba(255,255,255,0.04);border-radius:28px;backdrop-filter:blur(10px)}
.upload-visual{position:relative;width:120px;height:120px;margin:0 auto 28px}
.orbit-ring{position:absolute;border-radius:50%;border:1px solid rgba(6,182,212,0.1);animation:spin linear infinite}
.r1{inset:0;animation-duration:20s}
.r2{inset:10px;animation-duration:14s;animation-direction:reverse;border-color:rgba(99,102,241,0.1)}
.r3{inset:20px;animation-duration:8s;border-color:rgba(236,72,153,0.08)}
@keyframes spin{from{transform:rotate(0)}to{transform:rotate(360deg)}}
.upload-center-icon{position:absolute;inset:30px;border-radius:50%;background:rgba(6,182,212,0.06);display:flex;align-items:center;justify-content:center;color:#06b6d4}
.upload-title{font-size:26px;font-weight:800;color:#f1f5f9;letter-spacing:-0.02em}
.upload-sub{font-size:13px;color:#64748b;margin:8px 0 24px;line-height:1.5}

.drop-zone{
  display:flex;cursor:pointer;transition:all .3s;border-radius:20px;
  border:2px dashed rgba(255,255,255,0.06);overflow:hidden;
}
.drop-zone:hover,.drop-zone.active{border-color:rgba(6,182,212,0.3);background:rgba(6,182,212,0.02)}
.drop-inner{display:flex;flex-direction:column;align-items:center;padding:40px 24px;width:100%;gap:8px;color:#475569;transition:color .3s}
.drop-zone:hover .drop-inner{color:#06b6d4}
.drop-text{font-size:13px;font-weight:600;color:#94a3b8}
.drop-hint{font-size:10px;color:#334155}

.upload-features{display:flex;justify-content:center;gap:20px;margin-top:24px}
.uf{font-size:10px;font-weight:600;color:#475569;display:flex;align-items:center;gap:5px}
.uf-dot{width:6px;height:6px;border-radius:50%}
.d1{background:#06b6d4}.d2{background:#8b5cf6}.d3{background:#10b981}

/* ─── DASHBOARD ─── */
.dash-main{max-width:1600px;margin:0 auto;padding:24px 20px 80px;position:relative;z-index:1}

/* Header */
.dash-header{display:flex;justify-content:space-between;align-items:flex-end;flex-wrap:wrap;gap:16px;padding-bottom:24px;border-bottom:1px solid rgba(255,255,255,0.04);margin-bottom:4px}
.header-eyebrow{font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:0.3em;color:#06b6d4;margin-bottom:6px}
.dash-title{font-size:28px;font-weight:900;letter-spacing:-0.03em;color:#f1f5f9;line-height:1.15}
.dash-title span{color:#94a3b8;font-weight:600}
.header-right{display:flex;gap:12px}
.period-badge{
  display:flex;align-items:center;gap:10px;
  background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.05);
  padding:10px 16px;border-radius:14px;
}
.period-icon{color:#06b6d4;display:flex}
.period-label{display:block;font-size:8px;font-weight:700;text-transform:uppercase;letter-spacing:0.12em;color:#475569}
.period-value{display:block;font-size:13px;font-weight:700;color:#06b6d4;margin-top:1px}

/* Section heads */
.dash-section{margin-top:32px;animation:fadeUp .5s ease both}
.sec-head{display:flex;align-items:center;gap:12px;margin-bottom:18px}
.sec-num{
  font-family:'JetBrains Mono',monospace;font-size:10px;font-weight:600;
  color:#06b6d4;background:rgba(6,182,212,0.06);border:1px solid rgba(6,182,212,0.1);
  padding:4px 8px;border-radius:6px;letter-spacing:0.05em;
}
.sec-title{font-size:10px;font-weight:800;text-transform:uppercase;letter-spacing:0.3em;color:#64748b;white-space:nowrap}
.sec-line{flex:1;height:1px;background:linear-gradient(90deg,rgba(255,255,255,0.06),transparent 80%)}

@keyframes fadeUp{from{opacity:0;transform:translateY(16px)}to{opacity:1;transform:translateY(0)}}
.dash-section:nth-child(2){animation-delay:.04s}
.dash-section:nth-child(3){animation-delay:.08s}
.dash-section:nth-child(4){animation-delay:.12s}
.dash-section:nth-child(5){animation-delay:.16s}
.dash-section:nth-child(6){animation-delay:.2s}
.dash-section:nth-child(7){animation-delay:.24s}
.dash-section:nth-child(8){animation-delay:.28s}

/* ─── KPI CARDS ─── */
.kpi-grid{display:grid;grid-template-columns:repeat(6,1fr);gap:12px}
@media(max-width:1200px){.kpi-grid{grid-template-columns:repeat(3,1fr)}}
@media(max-width:640px){.kpi-grid{grid-template-columns:repeat(2,1fr)}}

.kpi-card{
  background:rgba(255,255,255,0.015);border:1px solid rgba(255,255,255,0.04);
  border-radius:16px;padding:18px;position:relative;overflow:hidden;
  transition:all .3s;
}
.kpi-card::after{
  content:'';position:absolute;top:0;left:0;right:0;height:2px;
  background:var(--kpi-color);opacity:0;transition:opacity .3s;
}
.kpi-card:hover{border-color:rgba(255,255,255,0.08);transform:translateY(-2px);box-shadow:0 8px 32px rgba(0,0,0,0.3)}
.kpi-card:hover::after{opacity:1}

.kpi-header{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:14px}
.kpi-label{font-size:9px;font-weight:700;text-transform:uppercase;letter-spacing:0.1em;color:#64748b}
.kpi-icon-box{width:28px;height:28px;border-radius:8px;display:flex;align-items:center;justify-content:center;background:var(--kpi-bg);color:var(--kpi-color)}
.kpi-icon-box svg{width:15px;height:15px}
.kpi-body{margin-bottom:12px}
.kpi-value{font-size:24px;font-weight:900;color:#f1f5f9;letter-spacing:-0.02em;font-feature-settings:'tnum'}
.kpi-sub{font-size:9px;color:#475569;margin-top:2px;font-weight:500}

.kpi-progress{display:flex;align-items:center;gap:8px}
.kpi-progress-track{flex:1;height:3px;border-radius:99px;background:rgba(255,255,255,0.04);overflow:hidden}
.kpi-progress-fill{height:100%;border-radius:99px;background:var(--kpi-color);transition:width 1.2s cubic-bezier(0.4,0,0.2,1)}
.kpi-pct{font-size:9px;font-weight:700;color:#475569;font-family:'JetBrains Mono',monospace;min-width:28px;text-align:right}

/* ─── RISK PANEL ─── */
.risk-panel{
  position:relative;overflow:hidden;
  background:linear-gradient(135deg,rgba(30,27,75,0.6),rgba(49,46,129,0.4));
  border:1px solid rgba(99,102,241,0.12);
  border-radius:22px;padding:32px;
}
.risk-decoration{position:absolute;inset:0;pointer-events:none;overflow:hidden}
.risk-circle{position:absolute;border-radius:50%;border:1px solid rgba(99,102,241,0.06)}
.c1{width:300px;height:300px;top:-100px;right:-80px;border-color:rgba(6,182,212,0.08)}
.c2{width:200px;height:200px;bottom:-60px;left:-40px;border-color:rgba(139,92,246,0.06)}

.risk-grid{position:relative;display:grid;grid-template-columns:repeat(3,1fr);gap:16px}
@media(max-width:768px){.risk-grid{grid-template-columns:1fr}}

.risk-card{
  background:rgba(255,255,255,0.03);border:1px solid rgba(255,255,255,0.06);
  border-radius:16px;padding:20px;transition:all .3s;
}
.risk-card:hover{border-color:rgba(255,255,255,0.1);background:rgba(255,255,255,0.05)}
.risk-card-header{display:flex;align-items:center;gap:8px;margin-bottom:12px;color:#a5b4fc;font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:0.1em}
.rc1{border-top:2px solid #ef4444}
.rc2{border-top:2px solid #8b5cf6}
.rc3{border-top:2px solid #f59e0b}

.risk-value{font-size:36px;font-weight:900;color:#fff}
.risk-desc{font-size:10px;color:#818cf8;margin-top:2px}
.risk-tags{display:flex;flex-wrap:wrap;gap:6px}
.risk-tag{padding:5px 12px;border-radius:8px;background:rgba(139,92,246,0.12);font-size:10px;font-weight:700;color:#c4b5fd;border:1px solid rgba(139,92,246,0.15)}
.risk-na{font-size:11px;color:#475569}
.risk-system{font-size:18px;font-weight:800;color:#fbbf24}

/* ─── CHART CARDS ─── */
.charts-2col{display:grid;grid-template-columns:repeat(2,1fr);gap:16px}
.charts-3col{display:grid;grid-template-columns:repeat(3,1fr);gap:16px}
@media(max-width:1024px){.charts-3col{grid-template-columns:1fr 1fr}}
@media(max-width:768px){.charts-2col,.charts-3col{grid-template-columns:1fr}}

.chart-card{
  background:rgba(255,255,255,0.015);border:1px solid rgba(255,255,255,0.04);
  border-radius:18px;padding:20px;transition:all .3s;
}
.chart-card:hover{border-color:rgba(255,255,255,0.08)}
.chart-card-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px}
.chart-card-head h4{font-size:12px;font-weight:700;color:#cbd5e1;letter-spacing:0.02em}
.chart-badge{font-size:8px;font-weight:700;text-transform:uppercase;letter-spacing:0.1em;padding:3px 8px;border-radius:6px}
.badge-cyan{background:rgba(6,182,212,0.1);color:#06b6d4}
.badge-violet{background:rgba(139,92,246,0.1);color:#8b5cf6}
.badge-pink{background:rgba(236,72,153,0.1);color:#ec4899}
.badge-amber{background:rgba(245,158,11,0.1);color:#f59e0b}
.badge-green{background:rgba(16,185,129,0.1);color:#10b981}
.badge-red{background:rgba(239,68,68,0.1);color:#ef4444}

.chart-body{height:280px;position:relative}
.chart-body canvas{width:100%!important;height:100%!important}
.chart-sq{height:260px}
.chart-wide{height:340px}

/* ─── TABLE ─── */
.table-toolbar{display:flex;align-items:center;justify-content:space-between;gap:12px;margin-bottom:12px;flex-wrap:wrap}
.search-box{
  display:flex;align-items:center;gap:8px;
  background:rgba(255,255,255,0.025);border:1px solid rgba(255,255,255,0.05);
  border-radius:12px;padding:8px 14px;flex:1;max-width:380px;transition:border-color .2s;
}
.search-box:focus-within{border-color:rgba(6,182,212,0.3)}
.search-box svg{color:#475569;flex-shrink:0}
.search-input{border:none;background:transparent;outline:none;color:#e2e8f0;font-size:12px;font-family:inherit;width:100%}
.search-input::placeholder{color:#334155}

.toolbar-right{display:flex;align-items:center;gap:16px;flex-wrap:wrap}
.status-legend{display:flex;gap:12px;flex-wrap:wrap}
.legend-item{display:flex;align-items:center;gap:4px;font-size:9px;font-weight:600;color:#64748b}
.legend-dot{width:6px;height:6px;border-radius:50%}
.dot-open{background:#fb923c}.dot-progress{background:#3b82f6}.dot-resolved{background:#10b981}.dot-closed{background:#06b6d4}
.table-count{font-size:10px;color:#475569;font-weight:600;font-family:'JetBrains Mono',monospace}

.table-container{overflow-x:auto;border-radius:14px;border:1px solid rgba(255,255,255,0.04);background:rgba(255,255,255,0.01)}
.data-table{width:100%;border-collapse:collapse;font-size:12px}
.data-table thead{background:rgba(6,10,20,0.8)}
.data-table th{
  padding:11px 12px;text-align:left;font-size:9px;font-weight:700;
  text-transform:uppercase;letter-spacing:0.1em;color:#64748b;
  border-bottom:1px solid rgba(255,255,255,0.04);white-space:nowrap;user-select:none;
}
.th-sort{cursor:pointer;transition:color .2s}
.th-sort:hover{color:#06b6d4}
.sort-indicator{color:#06b6d4;margin-left:3px;font-size:10px}

.data-table td{
  padding:9px 12px;color:#cbd5e1;border-bottom:1px solid rgba(255,255,255,0.02);
  white-space:nowrap;max-width:180px;overflow:hidden;text-overflow:ellipsis;
}
.data-table tbody tr{transition:background .15s}
.data-table tbody tr:hover{background:rgba(6,182,212,0.03)}
.row-open{border-left:2px solid #fb923c}
.td-key{font-family:'JetBrains Mono',monospace;font-weight:600;font-size:11px;color:#06b6d4}
.td-date{font-family:'JetBrains Mono',monospace;font-size:10px;color:#64748b}
.td-empty{text-align:center;padding:28px!important;color:#334155}

/* Chips */
.chip{
  display:inline-flex;align-items:center;padding:3px 10px;border-radius:6px;
  font-size:9px;font-weight:700;text-transform:uppercase;letter-spacing:0.04em;
  white-space:nowrap;
}
.chip-green{background:rgba(16,185,129,0.12);color:#34d399;border:1px solid rgba(16,185,129,0.15)}
.chip-teal{background:rgba(6,182,212,0.12);color:#22d3ee;border:1px solid rgba(6,182,212,0.15)}
.chip-blue{background:rgba(59,130,246,0.12);color:#60a5fa;border:1px solid rgba(59,130,246,0.15)}
.chip-amber{background:rgba(251,146,60,0.12);color:#fb923c;border:1px solid rgba(251,146,60,0.15)}
.chip-violet{background:rgba(139,92,246,0.12);color:#a78bfa;border:1px solid rgba(139,92,246,0.15)}
.chip-red{background:rgba(239,68,68,0.12);color:#f87171;border:1px solid rgba(239,68,68,0.15)}
.chip-orange{background:rgba(249,115,22,0.12);color:#fb923c;border:1px solid rgba(249,115,22,0.15)}
.chip-yellow{background:rgba(234,179,8,0.12);color:#facc15;border:1px solid rgba(234,179,8,0.15)}
.chip-slate{background:rgba(148,163,184,0.08);color:#94a3b8;border:1px solid rgba(148,163,184,0.1)}

/* Pagination */
.pagination{display:flex;align-items:center;justify-content:center;gap:6px;margin-top:16px}
.pg-btn{
  padding:6px 14px;border-radius:8px;font-size:11px;font-weight:600;
  color:#94a3b8;border:1px solid rgba(255,255,255,0.05);background:transparent;
  cursor:pointer;transition:all .2s;display:flex;align-items:center;gap:4px;
}
.pg-btn:hover:not(:disabled){color:#e2e8f0;border-color:rgba(6,182,212,0.2);background:rgba(6,182,212,0.04)}
.pg-btn:disabled{opacity:.3;cursor:not-allowed}
.pg-numbers{display:flex;gap:4px}
.pg-num{
  width:32px;height:32px;border-radius:8px;font-size:11px;font-weight:600;
  color:#64748b;border:1px solid transparent;background:transparent;
  cursor:pointer;transition:all .2s;display:flex;align-items:center;justify-content:center;
}
.pg-num:hover{color:#e2e8f0;background:rgba(255,255,255,0.03)}
.pg-num.active{color:#06b6d4;background:rgba(6,182,212,0.08);border-color:rgba(6,182,212,0.2)}
</style>