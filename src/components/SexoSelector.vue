<template>
  <div class="mb-4">
    <label :for="'sexo-' + uniqueId" class="block text-gray-700 text-sm font-bold mb-2">Sexo:</label>
    <select
      :id="'sexo-' + uniqueId"
      v-model="selectedSexo"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
    >
      <option value="" disabled>Sexo</option>
      <option v-for="sexo in sexos" :key="sexo.id_sexo" :value="sexo.id_sexo">
        {{ sexo.sexo }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'; // 'computed' no es necesario aquí

const props = defineProps({
  modelValue: {
    type: Number, // Espera el id_sexo como el valor
    default: null,
  },

});

const emit = defineEmits(['update:modelValue']);

const sexos = ref([]);
const loadingSexos = ref(true);
const selectedSexo = ref(props.modelValue); // Inicializa con el valor pasado por v-model
const uniqueId = Math.random().toString(36).substring(2, 15); // ID único para accesibilidad

const API_URL = import.meta.env.PUBLIC_API_URL;
const SEXO_API_URL = `${API_URL}/sexo`;

const obtenerSexos = async () => {
  try {
    const res = await fetch(SEXO_API_URL);
    if (!res.ok) {
      throw new Error(`Error al cargar tipos de sexo: ${res.statusText}`);
    }
    sexos.value = await res.json();
  } catch (error) {
    console.error('Error al cargar tipos de sexo:', error);
    // Puedes manejar el error aquí, por ejemplo, mostrando un mensaje al usuario
  } finally {
    loadingSexos.value = false;
  }
};

onMounted(obtenerSexos); // Cargar sexos al montar el componente

// Observa cambios en la selección local del usuario y emite el evento para v-model
watch(selectedSexo, (newValue) => {
  emit('update:modelValue', newValue);
});

// Observa cambios en la prop modelValue del padre para actualizar la selección local
// Esto es vital cuando el padre carga un usuario para edición y pasa un nuevo modelValue.
watch(
  () => props.modelValue,
  (newValue) => {
    selectedSexo.value = newValue;
  }
);
</script>