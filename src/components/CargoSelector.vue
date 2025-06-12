<template>
  <div class="mb-4">
    <label :for="'cargo-' + uniqueId" class="block text-gray-700 text-sm font-bold mb-2">Cargo:</label>
    <select
      :id="'cargo-' + uniqueId"
      v-model="selectedcargo"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
    >
      <option value="" disabled>cargo</option>
      <option v-for="cargo in cargos" :key="cargo.id_cargo" :value="cargo.id_cargo">
        {{ cargo.cargo }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'; // 'computed' no es necesario aquí

const props = defineProps({
  modelValue: {
    type: Number, // Espera el id_cargo como el valor
    default: null,
  },

});

const emit = defineEmits(['update:modelValue']);

const cargos = ref([]);
const loadingcargos = ref(true);
const selectedcargo = ref(props.modelValue); // Inicializa con el valor pasado por v-model
const uniqueId = Math.random().toString(36).substring(2, 15); // ID único para accesibilidad

const API_URL = import.meta.env.PUBLIC_API_URL;
const cargo_API_URL = `${API_URL}/cargo`;

const obtenercargos = async () => {
  try {
    const res = await fetch(cargo_API_URL);
    if (!res.ok) {
      throw new Error(`Error al cargar tipos de cargo: ${res.statusText}`);
    }
    cargos.value = await res.json();
  } catch (error) {
    console.error('Error al cargar tipos de cargo:', error);
    // Puedes manejar el error aquí, por ejemplo, mostrando un mensaje al usuario
  } finally {
    loadingcargos.value = false;
  }
};

onMounted(obtenercargos); // Cargar cargos al montar el componente

// Observa cambios en la selección local del usuario y emite el evento para v-model
watch(selectedcargo, (newValue) => {
  emit('update:modelValue', newValue);
});

// Observa cambios en la prop modelValue del padre para actualizar la selección local
// Esto es vital cuando el padre carga un usuario para edición y pasa un nuevo modelValue.
watch(
  () => props.modelValue,
  (newValue) => {
    selectedcargo.value = newValue;
  }
);
</script>