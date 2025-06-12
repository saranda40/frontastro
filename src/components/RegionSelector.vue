<template>
  <div class="mb-4">
    <label :for="'region-' + uniqueId" class="block text-gray-700 text-sm font-bold mb-2">Región:</label>
    <select
      :id="'region-' + uniqueId"
      v-model="selectedregion"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
    >
      <option value="" disabled>región</option>
      <option v-for="region in regions" :key="region.id_region" :value="region.id_region">
        {{ region.region }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'; // 'computed' no es necesario aquí

const props = defineProps({
  modelValue: {
    type: Number, // Espera el id_region como el valor
    default: null,
  },

});

const emit = defineEmits(['update:modelValue']);

const regions = ref([]);
const loadingregions = ref(true);
const selectedregion = ref(props.modelValue); // Inicializa con el valor pasado por v-model
const uniqueId = Math.random().toString(36).substring(2, 15); // ID único para accesibilidad

const API_URL = import.meta.env.PUBLIC_API_URL;
const region_API_URL = `${API_URL}/region`;

const obtenerregions = async () => {
  try {
    const res = await fetch(region_API_URL);
    if (!res.ok) {
      throw new Error(`Error al cargar tipos de region: ${res.statusText}`);
    }
    regions.value = await res.json();
  } catch (error) {
    console.error('Error al cargar tipos de region:', error);
    // Puedes manejar el error aquí, por ejemplo, mostrando un mensaje al usuario
  } finally {
    loadingregions.value = false;
  }
};

onMounted(obtenerregions); // Cargar regions al montar el componente

// Observa cambios en la selección local del usuario y emite el evento para v-model
watch(selectedregion, (newValue) => {
  emit('update:modelValue', newValue);
});

// Observa cambios en la prop modelValue del padre para actualizar la selección local
// Esto es vital cuando el padre carga un usuario para edición y pasa un nuevo modelValue.
watch(
  () => props.modelValue,
  (newValue) => {
    selectedregion.value = newValue;
  }
);
</script>