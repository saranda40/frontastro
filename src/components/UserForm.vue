<template>
  <div v-if="isVisible" class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center z-50 p-4">
    <div class="bg-white p-6 rounded-lg shadow-xl w-full max-w-2xl relative"> <button @click="cerrarFormulario" class="absolute top-3 right-3 text-gray-500 hover:text-gray-800 text-2xl font-bold">&times;</button>
      
      <h2 class="text-2xl font-bold text-gray-800 mb-6 text-center">
        {{ isEditing ? 'Editar Usuario' : 'Crear Usuario' }}
      </h2>

      <form @submit.prevent="guardarUsuario" class="space-y-4"> <div class="flex flex-col md:flex-row md:space-x-4">
          <div class="mb-4 md:mb-0 flex-grow">
            <label for="usuario" class="block text-gray-700 text-sm font-bold mb-2">Usuario:</label>
            <input type="text" id="usuario" v-model="form.usuario"
                   class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                   required>
          </div>
          <div class="flex items-center justify-end md:justify-start pt-7">
            <label class="block text-gray-700 text-sm font-bold mr-2">Activo:</label>
            <input type="checkbox" v-model="form.activo" class="mr-2 leading-tight h-5 w-5">
          </div>
        </div>

        <div class="flex flex-col md:flex-row md:space-x-4">
          <div class="mb-4 md:mb-0 w-full md:w-1/2">
            <label for="nombres" class="block text-gray-700 text-sm font-bold mb-2">Nombres:</label>
            <input type="text" id="nombres" v-model="form.nombres"
                   class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                   required>
          </div>
          <div class="w-full md:w-1/2">
            <label for="apellidos" class="block text-gray-700 text-sm font-bold mb-2">Apellidos:</label>
            <input type="text" id="apellidos" v-model="form.apellidos"
                   class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                   required>
          </div>
        </div>

        <div class="flex flex-col md:flex-row md:space-x-4">
          <div class="w-full md:w-1/3">
            <label for="fecha_nacimiento" class="block text-gray-700 text-sm font-bold mb-2">Fecha de Nacimiento:</label>
            <input type="date" id="fecha_nacimiento" v-model="form.fecha_nacimiento"
                   class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                   required>
          </div>
          <div class="w-full md:w-1/3">
            <SexoSelector v-model="form.id_sexo" class="mb-4 md:mb-0" />
          </div>
          <div class="w-full md:w-1/3">
            <EstadoCivilSelector v-model="form.id_estado_civil" class="mb-4 md:mb-0" />
          </div>
        </div>

        <div class="flex flex-col md:flex-row md:space-x-4">
           <div class="w-full md:w-1/3">
            <NacionalidadSelector v-model="form.id_nacionalidad" class="mb-4 md:mb-0" />
          </div>
          <div class="mb-4 md:mb-0 w-full md:w-1/3">
            <label for="email" class="block text-gray-700 text-sm font-bold mb-2">Email:</label>
            <input type="email" id="email" v-model="form.email"
                   class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                   required>
          </div>
          <div class="w-full md:w-1/3">
            <label for="telefono" class="block text-gray-700 text-sm font-bold mb-2">Teléfono:</label>
            <input type="text" id="telefono" v-model="form.telefono"
                   class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                   placeholder="Opcional">
          </div>
        </div>

        <div class="flex flex-col md:flex-row md:space-x-4">
          <div class="w-full md:w-1/2">
            <TipoDocSelector v-model="form.id_tipo_documento" class="mb-4 md:mb-0" />
          </div>
          <div class="w-full md:w-1/2">
            <label for="numero_documento" class="block text-gray-700 text-sm font-bold mb-2">Número Documento:</label>
            <input type="text" id="numero_documento" v-model="form.numero_documento"
                   class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                   placeholder="Opcional">
          </div>
        </div>

        <div>
          <CargoSelector v-model="form.id_cargo" />
        </div>

        <div class="mb-4">
          <label for="password" class="block text-gray-700 text-sm font-bold mb-2">Contraseña:</label>
          <input type="password" id="password" v-model="form.password"
                 class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                 required>
        </div>

        <div>
          <label for="direccion" class="block text-gray-700 text-sm font-bold mb-2">Dirección:</label>
          <input type="text" id="direccion" v-model="form.direccion"
                 class="shadow appearance-none border rounded-lg w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                 placeholder="Dirección">
        </div>

        <div class="flex flex-col md:flex-row md:space-x-4">
          <div class="w-full md:w-1/2">
            <RegionSelector v-model="form.id_region" class="mb-4 md:mb-0" />
          </div>
          <div class="w-full md:w-1/2">
            <ComunaSelector v-model="form.id_comuna" :idRegionSeleccionada="form.id_region" class="mb-4 md:mb-0" />
          </div>
        </div>
        
        <div class="flex items-center justify-end pt-4 space-x-4">
          <button type="button" @click="cerrarFormulario"
                  class="bg-gray-500 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded-lg focus:outline-none focus:shadow-outline">
            Cancelar
          </button>
          <button type="submit"
                  class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg focus:outline-none focus:shadow-outline">
            {{ isEditing ? 'Actualizar' : 'Guardar' }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, defineProps, defineEmits } from 'vue';
import SexoSelector from './SexoSelector.vue'; 
import TipoDocSelector from './TipoDocSelector.vue'; 
import EstadoCivilSelector from './EstadoCivilSelector.vue';
import NacionalidadSelector from './NacionalidadSelector.vue'; 
import CargoSelector from './CargoSelector.vue'; 
import RegionSelector from './RegionSelector.vue'; 
import ComunaSelector from './ComunaSelector.vue'; 

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  usuarioInicial: {
    type: Object,
    default: null // Será null para crear, o el objeto usuario para editar
  }
});

const emit = defineEmits(['cerrar', 'usuarioGuardado']);

const isVisible = ref(props.show);
const isEditing = ref(false);

const form = ref({
  id_usuario: null,
  usuario: '',
  nombres: '',
  apellidos: '',
  email: '',
  activo: true,
  telefono: '',
  numero_documento: '',
  id_tipo_documento: null,
  direccion: '',
  id_sexo: null,
  id_estado_civil: null,
  id_nacionalidad: null,
  id_cargo: null,
  password: '', // Añadido campo de contraseña
  id_region: null,       
  id_comuna: null,       
});

// Watcher para sincronizar 'isVisible' con 'props.show' y preparar el formulario
watch(() => props.show, (newVal) => {
  isVisible.value = newVal;
  if (newVal && props.usuarioInicial) {
    // Modo edición: Copiar datos del usuario inicial al formulario
    isEditing.value = true;
    form.value = { ...props.usuarioInicial };
    // Asegurarse de que el campo password no se prellene con el hash si viene del backend
    form.value.password = ''; // Limpiar la contraseña al editar por seguridad y UX
    
    // Asegurarse de que los campos específicos existan si el usuarioInicial los tiene
    form.value.id_sexo = props.usuarioInicial.id_sexo || null;
    form.value.id_estado_civil = props.usuarioInicial.id_estado_civil || null;
    form.value.id_nacionalidad = props.usuarioInicial.id_nacionalidad || null;
    form.value.id_cargo = props.usuarioInicial.id_cargo || null;
    form.value.id_tipo_documento = props.usuarioInicial.id_tipo_documento || null;
    form.value.telefono = props.usuarioInicial.telefono || '';
    form.value.numero_documento = props.usuarioInicial.numero_documento || '';
    form.value.direccion = props.usuarioInicial.direccion || '';
    form.value.id_region = props.usuarioInicial.id_region || null;
    form.value.id_comuna = props.usuarioInicial.id_comuna || null;

  } else if (newVal && !props.usuarioInicial) {
    // Modo creación: Reiniciar formulario a valores por defecto
    isEditing.value = false;
    form.value = {
      id_usuario: null,
      usuario: '',
      nombres: '',
      apellidos: '',
      email: '',
      activo: true,
      telefono: '',
      numero_documento: '',
      id_tipo_documento: null,
      direccion: '',
      id_sexo: null,
      id_estado_civil: null,
      id_nacionalidad: null,
      id_cargo: null,
      password: '', // Limpiar para nueva creación
      id_region: null,       
      id_comuna: null,       
    };
  }
}, { immediate: true });

const API_URL = import.meta.env.PUBLIC_API_URL;
const USUARIOS_API_URL = `${API_URL}/usuarios`;

const guardarUsuario = async () => {
  try {
    let res;
    // Crear una copia del formulario para enviar, quitando campos vacíos si es necesario
    const dataToSend = { ...form.value };

    // Lógica para no enviar la contraseña si se está editando y no ha sido modificada
    if (isEditing.value && dataToSend.password === '') {
        delete dataToSend.password; // No enviar la contraseña si no se cambió
    }

    if (isEditing.value) {
      res = await fetch(`${USUARIOS_API_URL}/${dataToSend.id_usuario}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(dataToSend) // Enviar la copia modificada
      });
    } else {
      res = await fetch(USUARIOS_API_URL, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(dataToSend) // Enviar la copia modificada
      });
    }

    if (!res.ok) {
      const errorData = await res.json();
      throw new Error(`Error en la API al guardar usuario: ${res.statusText} - ${JSON.stringify(errorData)}`);
    }

    const data = await res.json();
    emit('usuarioGuardado', data);
    cerrarFormulario();
  } catch (error) {
    console.error('Error al guardar usuario:', error);
    alert('Hubo un error al guardar el usuario. Consulta la consola para más detalles.');
  }
};

const cerrarFormulario = () => {
  isVisible.value = false;
  emit('cerrar');
};

// Limpiar id_comuna cuando id_region cambia
watch(() => form.value.id_region, (newRegionId, oldRegionId) => {
  if (newRegionId !== oldRegionId) {
    form.value.id_comuna = null; 
  }
});
</script>

<style scoped>
/* Ajustes para los componentes selectores para que hereden el rounded-lg */
/* Esto puede ser necesario si tus componentes selectores no aplican rounded-lg por defecto
   o si tienen estilos que sobrescriben los que aplicamos en el formulario principal. */
.rounded-lg {
  border-radius: 0.5rem; /* 8px */
}
</style>