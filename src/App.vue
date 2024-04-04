<template>
  <v-layout>
    <v-app-bar title="Base de datos">
  <template v-slot:prepend>
     <v-app-bar-nav-icon @click.stop="barOpen = !barOpen" ></v-app-bar-nav-icon>
  </template>
  <v-spacer></v-spacer>
</v-app-bar>  
    <v-navigation-drawer v-model="barOpen">
          <v-list>
              <v-list-item prepend-icon="mdi-vuetify" title="Meta 2.1" subtitle="Usando vuetify"></v-list-item>
          </v-list>
          <v-divider></v-divider>
          <v-list density="compact" nav>
            <v-list-item prepend-icon="mdi-home" link to="/" title="Inicio" value="Inicio"></v-list-item>
            <v-list-item prepend-icon="mdi-image-multiple" link to="/obtenerActivos" title="Activos" value="myfiles"></v-list-item>
            <v-list-item prepend-icon="mdi-image-multiple" link to="/obtenerUbicaciones" title="Ubicaciones" value="myfiles"></v-list-item>
            <v-list-item prepend-icon="mdi-image-multiple" link to="/obtenerResponsables" title="Responsables" value="myfiles"></v-list-item>
           </v-list>
      </v-navigation-drawer>

      <v-main style="height: 1000px">
          <RouterView />
      </v-main>
  </v-layout>
</template>

<script setup>
import { ref } from 'vue';
const barOpen = ref(false);
</script>
