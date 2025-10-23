<template>
  <div class="app-container">
    <header class="app-header">
      <h1 class="app-title">Dashboard de Gestión</h1>
    </header>
    
    <main class="main-content">
      <!-- Sección de Usuarios -->
      <UsuarioList 
        :usuarios="usuarios" 
        :api-url="USUARIOS_API_URL"
        @recargar-usuarios="cargarUsuarios"
        @mostrar-modal="mostrarModal"
      />

   

      <!-- Modal Global (sin cambios de diseño) -->
      <div v-if="showModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50" @click="showModal = false">
        <div class="bg-white p-8 rounded-lg shadow-xl max-w-sm w-full text-center" @click.stop>
          <h3 class="text-xl font-bold text-gray-800 mb-4">{{ modalTitle }}</h3>
          <p class="text-gray-600 mb-6">{{ modalMessage }}</p>
          <button @click="showModal = false" :class="modalVariant === 'success' ? 'bg-blue-500 hover:bg-blue-600' : 'bg-red-500 hover:bg-red-600'" class="w-full text-white py-2 rounded-lg transition">
            Entendido
          </button>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
// --- SIN CAMBIOS EN LA LÓGICA ---
// Todo el script se mantiene exactamente igual que en la versión anterior.
import { ref, onMounted } from 'vue';
import UsuarioList from './components/UsuarioList.vue';


interface Usuario { id_usuario: number; nombre: string; correo: string; rol: string; }


const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:4001';

const USUARIOS_API_URL = `${API_BASE_URL}/api/usuarios`;


const usuarios = ref<Usuario[]>([]);

const showModal = ref(false);
const modalTitle = ref('');
const modalMessage = ref('');
const modalVariant = ref('success');

const mostrarModal = (title: string, message: string, variant = 'success') => {
  modalTitle.value = title;
  modalMessage.value = message;
  modalVariant.value = variant;
  showModal.value = true;
};

const cargarUsuarios = async () => {
  try {
    const res = await fetch(USUARIOS_API_URL);
    
    if (!res.ok) throw new Error(`Error HTTP: ${res.status}`);
    usuarios.value = await res.json() as Usuario[];
  } catch (error: unknown) {
    let message = 'Ocurrió un error desconocido.';
    if (error instanceof Error) message = error.message;
    
    mostrarModal('❌ Error de Conexión', `No se pudo cargar la lista de usuarios. ${message}`, 'error');
  }
};



onMounted(() => {
  cargarUsuarios();
});
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap');

:root {
  --clr-background: #2c2f3a;        /* Fondo oscuro */
  --clr-card: #20232c;              /* Tarjeta más oscura */
  --clr-field: #fff;                /* Campo blanco */
  --clr-button: #e5a93b;            /* Botón amarillo */
  --clr-button-hover: #b87f21;      /* Botón hover más oscuro */
  --clr-text-primary: #fff;         /* Texto blanco */
  --clr-text-secondary: #c7c9d3;    /* Texto gris sutil */
  --clr-border: #47495a;            /* Bordes sutiles oscuros */
  --shadow: 0 4px 24px rgba(0,0,0,0.08);
}

body {
  font-family: 'Inter', sans-serif;
  background-color: var(--clr-background);
  color: var(--clr-text-primary);
  margin: 0;
  min-height: 100vh;
}

.form-card {
  background-color: var(--clr-card);
  border-radius: 0.5rem;
  box-shadow: var(--shadow);
  padding: 2rem 2.5rem 2.5rem 2.5rem;
  max-width: 400px;
  margin: 2rem auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.avatar {
  width: 110px;
  height: 110px;
  border-radius: 50%;
  background-color: #39a7a9;
  margin-top: -70px;
  margin-bottom: 1rem;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.form-group {
  width: 100%;
  margin-bottom: 1rem;
}

.form-group label {
  color: var(--clr-text-secondary);
  font-size: 0.95rem;
  margin-bottom: 0.2rem;
  display: block;
}

.form-group input {
  width: 100%;
  padding: 0.65rem;
  border: 1px solid var(--clr-border);
  border-radius: 0.3rem;
  font-size: 1rem;
  outline: none;
  background: var(--clr-field);
  color: #333;
  transition: border-color 0.2s, box-shadow 0.2s;
}

.form-group input:focus {
  border-color: var(--clr-button);
  box-shadow: 0 0 0 2px rgba(229,169,59,0.12);
}

.button-container {
  width: 100%;
  text-align: center;
  margin-top: 1rem;
}

.submit-button {
  background-color: var(--clr-button);
  color: #fff;
  padding: 0.7rem 2rem;
  border: none;
  border-radius: 0.3rem;
  font-size: 1.05rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.18s;
  box-shadow: 0 2px 7px rgba(0,0,0,0.12);
}

.submit-button:hover {
  background-color: var(--clr-button-hover);
}

@media (max-width: 500px) {
  .form-card {
    padding: 1rem;
    max-width: 95vw;
  }
}


</style>