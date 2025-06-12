<template>
  <div class="mb-4">
    <label :for="'estado_civil-' + uniqueId" class="block text-gray-700 text-sm font-bold mb-2">Estado Civil:</label>
    <select
      :id="'estado_civil-' + uniqueId"
      v-model="selectedestado_civil"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
    >
      <option value="" disabled>estado_civil</option>
      <option v-for="estado_civil in estado_civils" :key="estado_civil.id_estado_civil" :value="estado_civil.id_estado_civil">
        {{ estado_civil.estado_civil }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'; // 'computed' no es necesario aquí

const props = defineProps({
  modelValue: {
    type: Number, // Espera el id_estado_civil como el valor
    default: null,
  },

});

const emit = defineEmits(['update:modelValue']);

const estado_civils = ref([]);
const loadingestado_civils = ref(true);
const selectedestado_civil = ref(props.modelValue); // Inicializa con el valor pasado por v-model
const uniqueId = Math.random().toString(36).substring(2, 15); // ID único para accesibilidad

const API_URL = import.meta.env.PUBLIC_API_URL;
const estado_civil_API_URL = `${API_URL}/estado_civil`;

const obtenerestado_civils = async () => {
  try {
    const res = await fetch(estado_civil_API_URL);
    if (!res.ok) {
      throw new Error(`Error al cargar tipos de estado_civil: ${res.statusText}`);
    }
    estado_civils.value = await res.json();
  } catch (error) {
    console.error('Error al cargar tipos de estado_civil:', error);
    // Puedes manejar el error aquí, por ejemplo, mostrando un mensaje al usuario
  } finally {
    loadingestado_civils.value = false;
  }
};

onMounted(obtenerestado_civils); // Cargar estado_civils al montar el componente

// Observa cambios en la selección local del usuario y emite el evento para v-model
watch(selectedestado_civil, (newValue) => {
  emit('update:modelValue', newValue);
});

// Observa cambios en la prop modelValue del padre para actualizar la selección local
// Esto es vital cuando el padre carga un usuario para edición y pasa un nuevo modelValue.
watch(
  () => props.modelValue,
  (newValue) => {
    selectedestado_civil.value = newValue;
  }
);
</script>