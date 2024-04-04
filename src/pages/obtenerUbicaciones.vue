<template>
    <v-card flat title="Datos de las ubicaciones">
     <template v-slot:text>
       <v-text-field
         v-model="search"
         label="Buscar"
         prepend-inner-icon="mdi-magnify"
         single-line
         variant="outlined"
         hide-details
       ></v-text-field>
     </template>
 
     <v-data-table :headers="headers" :items="dataItems" :search="search">
  <template v-slot:item.activosAsociadosId="{ item }">
    <span v-for="(activo, index) in item.activosAsociadosId" :key="index">
      {{ activo }}{{ index < item.activosAsociadosId.length - 1 ? ', ' : '' }}
    </span>
  </template>
</v-data-table>
   </v-card>
 
   <v-container class="d-flex align-center justify-center">
     <v-btn @click="abrirDialogoAgregarUbicacion">Añadir ubicacion</v-btn>
     <v-btn @click="abrirDialogoEliminarUbicacion">Borrar ubicacion</v-btn>
     <v-btn @click="abrirDialogoActualizarUbicacion">Actualizar ubicacion</v-btn>
   </v-container>
 
   <v-dialog v-model="dialogAgregarUbicacion" max-width="500">
     <v-card>
       <v-card-title>Añadir Nueva Ubicacion</v-card-title>
       <v-card-text>
         <v-text-field v-model="nuevaUbicacion.id" label="ID"></v-text-field>
         <v-text-field v-model="nuevaUbicacion.descripcion" label="Descripcion de la ubicación"></v-text-field>
         <v-text-field v-model="nuevaUbicacion.activosAsociadosId" label="Activos custodiados"></v-text-field>
         <v-text-field v-model="nuevaUbicacion.imagen" label="Imagen"></v-text-field>
       </v-card-text>
       <v-card-actions>
         <v-btn color="primary" @click="agregarUbicacion">Agregar</v-btn>
         <v-btn color="error" @click="cerrarDialogoAgregarUbicacion">Cancelar</v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog>
 
   <v-dialog v-model="dialogBorrarUbicacion" max-width="500">
     <v-card>
       <v-card-title>Eliminar Ubicacion Por ID</v-card-title>
       <v-card-text>
         <v-text-field v-model="ubicacionABorrar.id" label="ID"></v-text-field>
       </v-card-text>
       <v-card-actions>
         <v-btn color="primary" @click="eliminarUbicacion(ubicacionABorrar.id)">Eliminar</v-btn>
         <v-btn color="error" @click="cerrarDialogoEliminarUbicacion">Cancelar</v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog>
 
   <v-dialog v-model="dialogActualizarUbicacion" max-width="500">
     <v-card>
       <v-card-title>Actualizar Ubicacion Por ID</v-card-title>
       <v-card-text>
        <v-text-field v-model="ubicacionAActualizar.id" label="ID"></v-text-field>
         <v-text-field v-model="ubicacionAActualizar.descripcion" label="Descripcion"></v-text-field>
         <v-text-field v-model="ubicacionAActualizar.activosAsociadosId" label="Activos custodiados"></v-text-field>
         <v-text-field v-model="ubicacionAActualizar.imagen" label="Imagen"></v-text-field>
        </v-card-text>
       <v-card-actions>
         <v-btn color="primary" @click="actualizarUbicacion(ubicacionAActualizar.id)">Actualizar</v-btn>
         <v-btn color="error" @click="cerrarDialogoActualizarUbicacion">Cancelar</v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog> 
   </template>
   
   <script>
   
   export default {
     name: 'obtenerUbicaciones',
     data() {
       return {
         search: '',
         dataItems: [],
         headers: [
           { text: 'ID', value: 'id'},
           { text: 'Descripcion', value: 'descripcion'},
           { text: 'Activos custodiados', value: 'activosAsociadosId', align: 'left', sortable: false },
           { text: 'Imagen', value: 'imagen'},
         ],
         dialogAgregarUbicacion: false,
         dialogBorrarUbicacion: false,
         dialogActualizarUbicacion: false,
         nuevaUbicacion: {
           id: '',
           descripcion: '',
           activosAsociadosId: '',
           imagen: ''
         },
         ubicacionABorrar: {
           id: ''
         },
         ubicacionAActualizar: {
           id: '',
           descripcion: '',
           activosAsociadosId: '',
           imagen: ''
         }
       };
     },
     mounted() {
       this.getUbicaciones();
     },
     methods: {
   async getUbicaciones() {
   try {
     const response = await fetch('https://localhost:4000/ubicaciones', {
             method: 'GET',
             mode: 'cors',
             credentials: 'same-origin', // o 'include' si estás utilizando cookies
         });
     if (!response.ok) {
       throw new Error('Network response was not ok');
     }
     const data = await response.json();
     this.dataItems = data;
   } catch (error) {
     console.error('Error fetching data:', error);
   }
 },
   async eliminarUbicacion(id) {
     try {
       const response = await fetch(`https://localhost:4000/ubicaciones/eliminarPorId/${id}`, {
         method: 'DELETE',
         mode: 'cors',
         credentials: 'same-origin',
       });
       this.getUbicaciones();
       if(!response.ok){
         throw new Error('Error al borrar la ubicacion');
       }
     } catch (error) {
       console.error('Error fetching data:', error);
     }
   },
   async agregarUbicacion() {
     try {
       const response = await fetch(`https://localhost:4000/ubicaciones`, {
         method: 'POST',
         mode: 'cors',
         headers: {
         'Content-Type': 'application/json' // Indica que estás enviando datos en formato JSON
         },
         body: JSON.stringify({
           id: this.nuevaUbicacion.id,
           descripcion: this.nuevaUbicacion.descripcion,
           activosAsociadosId: this.nuevaUbicacion.activosAsociadosId,
           imagen: this.nuevaUbicacion.imagen
         }),
         credentials: 'same-origin',
       });
       this.getUbicaciones();
       if(response.status==400){
         throw new Error('Error al agregar la ubicacion, ese ID ya existe');
       }
       console.log(response.status);
     } catch (error) {
       console.error('Error fetching data:', error);
     }
   },
   async actualizarUbicacion(id) {
     try {
       const existe = await fetch(`https://localhost:4000/ubicaciones/buscarPorId/${id}`, {
         method: 'GET',
         mode: 'cors',
         credentials: 'same-origin'
       });
       console.log(existe);
       if(existe.status==404){
         throw new Error('Error al verificar la existencia de la ubicacion');
       }
 
       const response = await fetch(`https://localhost:4000/ubicaciones/actualizarPorId/${id}`, {
         method: 'PUT',
         mode: 'cors',
         headers: {
         'Content-Type': 'application/json' // Indica que estás enviando datos en formato JSON
         },
         body: JSON.stringify({
           descripcion: this.ubicacionAActualizar.descripcion,
           activosAsociadosId: this.ubicacionAActualizar.activosAsociadosId,
           imagen: this.ubicacionAActualizar.imagen
         }),
         credentials: 'same-origin',
       });
       console.log(response.body);
       this.getUbicaciones();
       console.log(response.status);
       if(response.status==404){
         throw new Error('Error al actualizar la ubicacion, ese ID no existe');
       }
     } catch (error) {
       console.error('Error fetching data:', error);
     }
   },
     abrirDialogoAgregarUbicacion() {
       this.dialogAgregarUbicacion = true;
     },
     cerrarDialogoAgregarUbicacion() {
       this.dialogAgregarUbicacion = false;
     },
     abrirDialogoEliminarUbicacion() {
       this.dialogBorrarUbicacion = true;
     },
     cerrarDialogoEliminarUbicacion() {
       this.dialogBorrarUbicacion = false;
     },
     abrirDialogoActualizarUbicacion() {
       this.dialogActualizarUbicacion = true;
     },
     cerrarDialogoActualizarUbicacion() {
       this.dialogActualizarUbicacion = false;
     }
 },
   };
   </script>