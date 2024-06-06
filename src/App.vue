<template>
  <v-layout>
    <v-app-bar title="Base de datos">
      <template v-slot:prepend>
        <v-app-bar-nav-icon @click.stop="barOpen = !barOpen"></v-app-bar-nav-icon>
      </template>
      <v-spacer></v-spacer>
    </v-app-bar>
    
    <v-navigation-drawer v-model="barOpen">
      <v-list>
        <v-list-item prepend-icon="mdi-vuetify" title="Base de datos"></v-list-item>
        <v-list-item v-if="isAuthenticated">
          <v-list-item-content>
            <v-list-item-title><span v-text="nombreUsuario"></span></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
      
      <v-divider></v-divider>
      
      <v-list density="compact" nav>
        <v-list-item prepend-icon="mdi-home" link to="/" title="Inicio"></v-list-item>
        <v-list-item v-if="isAuthenticated" prepend-icon="mdi-image-multiple" link to="/obtenerActivos" title="Activos"></v-list-item>
        <v-list-item v-if="isAuthenticated" prepend-icon="mdi-image-multiple" link to="/obtenerUbicaciones" title="Ubicaciones"></v-list-item>
        <v-list-item v-if="isAuthenticated" prepend-icon="mdi-image-multiple" link to="/obtenerResponsables" title="Responsables"></v-list-item>
        <v-list-item v-if="!isAuthenticated" prepend-icon="mdi-login" @click="ingresar" title="Autentificación"></v-list-item>
        <v-list-item v-if="isAuthenticated" prepend-icon="mdi-logout" @click="cerrarSesion" title="Cerrar Sesión"></v-list-item>
      </v-list>
    </v-navigation-drawer>
    
    <v-main style="height: 1000px">
      Hola mundo
    </v-main>
  </v-layout>
</template>


<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';

const barOpen = ref(false);
const nombreUsuario = ref('');
const isAuthenticated = ref(false);
const router = useRouter();

const ingresar = async () => {
  window.location.href = 'https://localhost:4000/auth/google/';
};

const cerrarSesion = async () => {
  localStorage.removeItem('jwtToken');
  localStorage.removeItem('nombreUsuario');
  isAuthenticated.value = false;
  nombreUsuario.value = '';
  window.location.href = '/';
};

const handleGoogleCallback = async () => {
  const query = new URLSearchParams(window.location.search);
  const token = query.get('token');
  const name = query.get('name');

  console.log(`token: ${token}`);
  console.log(`name: ${name}`);

  if (token && name) {
    localStorage.setItem('jwtToken', token);
    localStorage.setItem('nombreUsuario', decodeURIComponent(name));
    nombreUsuario.value = decodeURIComponent(name);
    isAuthenticated.value = true;
    router.push("/");
  }
};

onMounted(async () => {
  const storedName = localStorage.getItem('nombreUsuario');
  if (storedName) {
    nombreUsuario.value = storedName;
    isAuthenticated.value = true;
  }
  await handleGoogleCallback();
});
</script>
