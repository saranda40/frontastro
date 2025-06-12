Gestión de Usuarios
Este proyecto es una aplicación web para la gestión de usuarios, desarrollada con Vue.js 3 y estilizada con Tailwind CSS. Permite crear, leer, actualizar y eliminar (CRUD) registros de usuarios, con un enfoque en una interfaz de usuario moderna y responsiva.

Características Principales
CRUD Completo: Permite crear nuevos usuarios, visualizar la lista existente, editar la información de usuarios y eliminarlos.
Interfaz Responsiva: Diseñada con Tailwind CSS para adaptarse a diferentes tamaños de pantalla (escritorio y móvil).
Formularios Dinámicos: Incluye selectores encadenados (ej. Región y Comuna) donde las opciones de un campo dependen de la selección en otro, mejorando la experiencia de usuario.
Componentización: Desarrollado con una arquitectura de componentes reutilizables en Vue.js (selectores de Sexo, Región, Comuna, etc.).
Integración con API REST: Se conecta a un backend (no incluido en este código, pero referenciado por PUBLIC_API_URL) para la persistencia de datos.
Tecnologías Utilizadas
Vue.js 3 (con Composition API y <script setup>): Framework progresivo para construir interfaces de usuario.
Tailwind CSS: Framework CSS de utilidad para un diseño rápido y altamente personalizable.
JavaScript (ES6+): Lenguaje de programación principal.
Vite: Herramienta de construcción rápida para proyectos Vue (asumido por import.meta.env.PUBLIC_API_URL).
Configuración del Proyecto
Prerrequisitos
Asegúrate de tener instalado Node.js y npm (o Yarn).

Instalación
Clona este repositorio:
Bash

git clone <URL_DEL_REPOSITORIO>
cd nombre-del-proyecto
Instala las dependencias:
Bash

npm install
# o
yarn install
Variables de Entorno
Crea un archivo .env en la raíz del proyecto con la siguiente variable:

PUBLIC_API_URL="http://127.0.0.1:8000/api/v1"
Asegúrate de reemplazar http://127.0.0.1:8000/api/v1 con la URL base de tu API de backend.

Servidor de Desarrollo
Para iniciar el servidor de desarrollo:

Bash

npm run dev
# o
yarn dev
Esto iniciará la aplicación en modo desarrollo, usualmente en http://localhost:5173/.

Construcción para Producción
Para generar una versión optimizada para producción:

Bash

npm run build
# o
yarn build
Los archivos compilados se guardarán en el directorio dist/.

Estructura de Componentes Clave
UserForm.vue: El componente principal del formulario para crear y editar usuarios, que orquesta la interacción entre los selectores.
SexoSelector.vue: Selector para el sexo del usuario.
RegionSelector.vue: Selector para la región.
ComunaSelector.vue: Selector para la comuna, cuyas opciones se filtran dinámicamente según la región seleccionada.
EstadoCivilSelector.vue: Selector para el estado civil.
NacionalidadSelector.vue: Selector para la nacionalidad.
CargoSelector.vue: Selector para el cargo.
TipoDocSelector.vue: Selector para el tipo de documento.
API Backend (Consideraciones)
Este frontend espera una API RESTful con los siguientes endpoints (ejemplos):

GET /api/v1/usuarios: Obtener todos los usuarios.
POST /api/v1/usuarios: Crear un nuevo usuario.
PUT /api/v1/usuarios/:id: Actualizar un usuario existente.
DELETE /api/v1/usuarios/:id: Eliminar un usuario.
GET /api/v1/sexo: Obtener tipos de sexo.
GET /api/v1/region: Obtener regiones.
GET /api/v1/comuna/region/:region_id: Obtener comunas filtradas por ID de región.
GET /api/v1/estado_civil: Obtener estados civiles.
GET /api/v1/nacionalidad: Obtener nacionalidades.
GET /api/v1/cargo: Obtener cargos.
GET /api/v1/tipo_documento: Obtener tipos de documento.
