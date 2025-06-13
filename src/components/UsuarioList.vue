<template>
  <div class="bg-gray-100 p-8 rounded shadow-md">
    <h2 class="text-3xl font-bold text-gray-800 mb-6 text-center">Lista de Usuarios</h2>
    <div class="mb-4 text-right">
      <button @click="abrirFormularioCrear" class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none dark:focus:ring-blue-800">
        Crear Usuario
      </button>
    </div>
    <div v-if="loading" class="text-gray-500">Cargando usuarios...</div>
    <div v-else>
      <div class="relative overflow-x-auto">
        <table class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
          <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
            <tr>
              <th scope="col" class="px-6 py-3">ID</th>
              <th scope="col" class="px-6 py-3">Usuario</th>
              <th scope="col" class="px-6 py-3">Nombres</th>
              <th scope="col" class="px-6 py-3">Apellidos</th>
              <th scope="col" class="px-6 py-3">Activo</th>
              <th scope="col" class="px-6 py-3">Email</th>
              <th scope="col" class="px-6 py-3">Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="usuario in usuarios" :key="usuario.id_usuario" class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 border-gray-200">
              <td class="px-6 py-4">{{ usuario.id_usuario }}</td>
              <td class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white">{{ usuario.usuario }}</td>
              <td class="px-6 py-4">{{ usuario.nombres }}</td>
              <td class="px-6 py-4">{{ usuario.apellidos }}</td>
              <td class="px-6 py-4">
                <span :class="usuario.activo ? 'text-green-500' : 'text-red-500'">
                  {{ usuario.activo ? 'Sí' : 'No' }}
                </span>
              </td>
              <td class="px-6 py-4">{{ usuario.email }}</td>
              <td class="px-6 py-4 space-x-2">
                <button @click="editarUsuario(usuario)" class="focus:outline-none text-black bg-yellow-400 hover:bg-yellow-500 focus:ring-4 focus:ring-yellow-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 dark:focus:ring-yellow-900">Editar</button>
                <button @click="eliminar(usuario.id_usuario)" class="focus:outline-none text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 dark:bg-red-600 dark:hover:bg-red-700 dark:focus:ring-red-900">Eliminar</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>

  <UserForm
    :show="showUserForm"
    :usuarioInicial="usuarioSeleccionado"
    @cerrar="showUserForm = false"
    @usuarioGuardado="handleUsuarioGuardado"
  />
</template>

<script setup>
import { ref, onMounted } from 'vue';
import UserForm from './UserForm.vue'; // Asegúrate de que la ruta sea correcta

// Variables de entorno (desde Astro)
const API_URL = import.meta.env.PUBLIC_API_URL;

const usuarios = ref([]);
const loading = ref(true);

// Estados para el popup del formulario
const showUserForm = ref(false);
const usuarioSeleccionado = ref(null); // Almacena el usuario a editar, o null para crear

const obtenerUsuarios = async () => {
  try {
    loading.value = true; // Mostrar cargando al inicio de la carga
    const res = await fetch(`${API_URL}/usuarios`);
    if (!res.ok) {
        throw new Error(`Error al obtener usuarios: ${res.statusText}`);
    }
    usuarios.value = await res.json();
  } catch (err) {
    console.error('Error al cargar usuarios:', err);
  } finally {
    loading.value = false;
  }
};

const eliminar = async (id) => {
  if (confirm('¿Estás seguro de eliminar este usuario?')) {
    try {
      const res = await fetch(`${API_URL}/usuarios/${id}`, {
        method: 'DELETE'
      });
      if (!res.ok) {
          throw new Error(`Error al eliminar usuario: ${res.statusText}`);
      }
      usuarios.value = usuarios.value.filter(u => u.id_usuario !== id); // Cambiado a id_usuario
      alert('Usuario eliminado con éxito.');
    } catch (err) {
      console.error('Error al eliminar usuario:', err);
      alert('Hubo un error al eliminar el usuario. Consulta la consola para más detalles.');
    }
  }
};

// Abre el formulario para crear un nuevo usuario
const abrirFormularioCrear = () => {
  usuarioSeleccionado.value = null; // No hay usuario para editar
  showUserForm.value = true;
};

// Abre el formulario para editar un usuario existente
const editarUsuario = (usuario) => {
  usuarioSeleccionado.value = usuario; // Pasa el objeto usuario completo
  showUserForm.value = true;
};

// Maneja el evento cuando un usuario es guardado/actualizado en el formulario
const handleUsuarioGuardado = (usuarioGuardado) => {
  // Aquí debes actualizar la lista de usuarios.
  // Si es un usuario nuevo, agregarlo a la lista.
  // Si es un usuario existente, actualizarlo en la lista.

  const index = usuarios.value.findIndex(u => u.id_usuario === usuarioGuardado.id_usuario);
  if (index !== -1) {
    // Usuario existente, actualizar
    usuarios.value[index] = usuarioGuardado;
    alert('Usuario actualizado con éxito.');
  } else {
    // Nuevo usuario, agregar al principio o al final
    usuarios.value.push(usuarioGuardado); // O usuarios.value.unshift(usuarioGuardado)
    alert('Usuario creado con éxito.');
  }
  // No necesitas volver a cargar todos los usuarios a menos que sea muy complejo el estado
  // obtenerUsuarios(); // Si prefieres recargar toda la lista
};

onMounted(obtenerUsuarios);
</script>

<style scoped>
/* Puedes agregar estilos específicos para este componente aquí si los necesitas */
</style>