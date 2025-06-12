<template>
  <div class="mb-4">
    <label :for="'comuna-' + uniqueId" class="block text-gray-700 text-sm font-bold mb-2">Comuna:</label>
    <select
      :id="'comuna-' + uniqueId"
      v-model="selectedComuna"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
      :disabled="loadingComunas || !idRegionSeleccionada || (comunas.length === 0 && !loadingComunas)"
    >
      <option value="" disabled>Seleccionar comuna</option>
      <option v-if="loadingComunas" disabled>Cargando comunas...</option>
      <option v-else-if="!idRegionSeleccionada" disabled>Seleccione una región primero</option>
      <option v-else-if="comunas.length === 0 && !loadingComunas" disabled>No hay comunas para esta región</option>
      <option v-for="comuna in comunas" :key="comuna.id_comuna" :value="comuna.id_comuna">
        {{ comuna.comuna }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';

const props = defineProps({
  modelValue: { // id_comuna actual del usuario (el que viene de usuarioInicial)
    type: Number,
    default: null,
  },
  idRegionSeleccionada: { // ID de la región seleccionada desde el padre
    type: Number,
    default: null,
  },
});

const emit = defineEmits(['update:modelValue']);

const comunas = ref([]); 
const loadingComunas = ref(false); 
const selectedComuna = ref(null); // Inicializamos a null. La lógica de selección se hará después.

const uniqueId = Math.random().toString(36).substring(2, 15);

const API_URL = import.meta.env.PUBLIC_API_URL;

const obtenerComunasPorRegion = async (regionId) => {
  if (!regionId) {
    comunas.value = [];
    selectedComuna.value = null; // También limpiar la selección si no hay región
    return;
  }
  loadingComunas.value = true;
  try {
    const res = await fetch(`${API_URL}/comuna/${regionId}`); // Asegúrate de que esta URL funcione en tu API
    if (!res.ok) {
      throw new Error(`Error al cargar comunas para la región ${regionId}: ${res.statusText}`);
    }
    comunas.value = await res.json();
    
    // *** Lógica clave para la selección inicial ***
    // Después de cargar las comunas, intentar seleccionar el modelValue
    if (props.modelValue !== null && comunas.value.some(c => c.id_comuna === props.modelValue)) {
      selectedComuna.value = props.modelValue;
    } else {
      selectedComuna.value = null; // Si el modelValue no es válido o no está en la lista, limpiar selección
    }

  } catch (error) {
    console.error('Error al cargar comunas:', error);
    comunas.value = []; // En caso de error, limpiar la lista
    selectedComuna.value = null; // Limpiar selección en caso de error
  } finally {
    loadingComunas.value = false;
  }
};

// 1. Observa cambios en la prop 'idRegionSeleccionada' y carga las comunas
watch(() => props.idRegionSeleccionada, (newRegionId, oldRegionId) => {
  // Reiniciar la comuna seleccionada a null inmediatamente si la región cambia
  // Esto evita que se muestre brevemente una comuna incorrecta al cambiar de región
  if (newRegionId !== oldRegionId) {
    selectedComuna.value = null; // Esto también emitirá un `update:modelValue` con null al padre
  }
  // Llamar a la función para obtener las comunas para la nueva región
  obtenerComunasPorRegion(newRegionId);
}, { immediate: true }); // 'immediate: true' para que se ejecute una vez al montar si ya hay una región

// 2. Observa cambios en la selección local del usuario (interacción con el select)
// y emite el evento para el v-model al componente padre.
watch(selectedComuna, (newValue) => {
  emit('update:modelValue', newValue);
});

// 3. Observa cambios en la prop 'modelValue' que viene del padre (por ejemplo, al cargar un usuario para edición)
// Asegura que si el padre pasa un nuevo 'modelValue' (id_comuna), este se intente seleccionar.
watch(
  () => props.modelValue,
  (newModelValue) => {
    // Solo actualiza si el newModelValue es diferente de la selección actual
    // Y si la lista de comunas ya está cargada y el newModelValue es válido en ella
    if (newModelValue !== selectedComuna.value) {
      if (newModelValue !== null && comunas.value.some(c => c.id_comuna === newModelValue)) {
        selectedComuna.value = newModelValue;
      } else if (newModelValue === null) {
        selectedComuna.value = null; // Si el padre explícitamente pone modelValue a null, también aquí
      }
    }
  }
);
</script>
