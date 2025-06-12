<template>
  <div class="mb-4">
    <label :for="'nacionalidad-' + uniqueId" class="block text-gray-700 text-sm font-bold mb-2">País:</label>
    <select
      :id="'nacionalidad-' + uniqueId"
      v-model="selectednacionalidad"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
    >
      <option value="" disabled>Nacionalidad</option>
      <option v-for="nacionalidad in nacionalidads" :key="nacionalidad.id_nacionalidad" :value="nacionalidad.id_nacionalidad">
        {{ nacionalidad.nacionalidad }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'; // 'computed' no es necesario aquí

const props = defineProps({
  modelValue: {
    type: Number, // Espera el id_nacionalidad como el valor
    default: null,
  },

});

const emit = defineEmits(['update:modelValue']);

const nacionalidads = ref([]);
const loadingnacionalidads = ref(true);
const selectednacionalidad = ref(props.modelValue); // Inicializa con el valor pasado por v-model
const uniqueId = Math.random().toString(36).substring(2, 15); // ID único para accesibilidad

const API_URL = import.meta.env.PUBLIC_API_URL;
const nacionalidad_API_URL = `${API_URL}/nacionalidad`;

const obtenernacionalidads = async () => {
  try {
    const res = await fetch(nacionalidad_API_URL);
    if (!res.ok) {
      throw new Error(`Error al cargar tipos de nacionalidad: ${res.statusText}`);
    }
    nacionalidads.value = await res.json();
  } catch (error) {
    console.error('Error al cargar tipos de nacionalidad:', error);
    // Puedes manejar el error aquí, por ejemplo, mostrando un mensaje al usuario
  } finally {
    loadingnacionalidads.value = false;
  }
};

onMounted(obtenernacionalidads); // Cargar nacionalidads al montar el componente

// Observa cambios en la selección local del usuario y emite el evento para v-model
watch(selectednacionalidad, (newValue) => {
  emit('update:modelValue', newValue);
});

// Observa cambios en la prop modelValue del padre para actualizar la selección local
// Esto es vital cuando el padre carga un usuario para edición y pasa un nuevo modelValue.
watch(
  () => props.modelValue,
  (newValue) => {
    selectednacionalidad.value = newValue;
  }
);
</script>