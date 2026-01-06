<template>
  <div class="p-8 border-2 border-dashed border-blue-200 rounded-xl bg-blue-50 text-center cursor-pointer hover:bg-blue-100 transition-all"
       @dragover.prevent @drop.prevent="handleDrop">
    <input type="file" ref="fileInput" class="hidden" accept=".csv" @change="handleFileSelect">
    
    <div @click="$refs.fileInput.click()">
      <p class="text-lg font-semibold text-blue-700">
        Arrastra aquí tu CSV o <span class="underline">haz clic para buscar</span>
      </p>
      <p class="text-sm text-blue-500 mt-2">Soporta reportes de Operatividad General y Manufactura</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import Papa from 'papaparse';

const emit = defineEmits(['data-ready']);

const processFile = (file) => {
  Papa.parse(file, {
    header: true,
    skipEmptyLines: true,
    complete: (results) => {
      const data = results.data;
      // Lógica de detección: 55 registros = General, 2 registros = Manufactura
      let type = 'desconocido';
      if (data.length > 10) type = 'general';
      else if (data.length > 0) type = 'manufactura';
      
      emit('data-ready', { type, rows: data });
    }
  });
};

const handleFileSelect = (e) => {
  const file = e.target.files[0];
  if (file) processFile(file);
};

const handleDrop = (e) => {
  const file = e.dataTransfer.files[0];
  if (file) processFile(file);
};
</script>