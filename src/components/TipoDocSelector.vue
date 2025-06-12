<template>
  <div class="mb-4">
    <label :for="'tipo_documento-' + uniqueId" class="block text-gray-700 text-sm font-bold mb-2">Tipo Documento:</label>
    <select
      :id="'tipo_documento-' + uniqueId"
      v-model="selectedtipo_documento"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
    >
      <option value="" disabled>tipo_documento</option>
      <option v-for="tipo_documento in tipo_documentos" :key="tipo_documento.id_tipo_documento" :value="tipo_documento.id_tipo_documento">
        {{ tipo_documento.tipo_documento }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'; // 'computed' no es necesario aquí

const props = defineProps({
  modelValue: {
    type: Number, // Espera el id_tipo_documento como el valor
    default: null,
  },

});

const emit = defineEmits(['update:modelValue']);

const tipo_documentos = ref([]);
const loadingtipo_documentos = ref(true);
const selectedtipo_documento = ref(props.modelValue); // Inicializa con el valor pasado por v-model
const uniqueId = Math.random().toString(36).substring(2, 15); // ID único para accesibilidad

const API_URL = import.meta.env.PUBLIC_API_URL;
const tipo_documento_API_URL = `${API_URL}/tipo_documento`;

const obtenertipo_documentos = async () => {
  try {
    const res = await fetch(tipo_documento_API_URL);
    if (!res.ok) {
      throw new Error(`Error al cargar tipos de tipo_documento: ${res.statusText}`);
    }
    tipo_documentos.value = await res.json();
  } catch (error) {
    console.error('Error al cargar tipos de tipo_documento:', error);
    // Puedes manejar el error aquí, por ejemplo, mostrando un mensaje al usuario
  } finally {
    loadingtipo_documentos.value = false;
  }
};

onMounted(obtenertipo_documentos); // Cargar tipo_documentos al montar el componente

// Observa cambios en la selección local del usuario y emite el evento para v-model
watch(selectedtipo_documento, (newValue) => {
  emit('update:modelValue', newValue);
});

// Observa cambios en la prop modelValue del padre para actualizar la selección local
// Esto es vital cuando el padre carga un usuario para edición y pasa un nuevo modelValue.
watch(
  () => props.modelValue,
  (newValue) => {
    selectedtipo_documento.value = newValue;
  }
);
</script>