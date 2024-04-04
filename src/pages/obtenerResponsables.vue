<template>
    <v-card flat title="Datos de los responsables">
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
  <template v-slot:item.activosCustodiados="{ item }">
    <span v-for="(activo, index) in item.activosCustodiados" :key="index">
      {{ activo }}{{ index < item.activosCustodiados.length - 1 ? ', ' : '' }}
    </span>
  </template>
</v-data-table>
   </v-card>
 
   <v-container class="d-flex align-center justify-center">
     <v-btn @click="abrirDialogoAgregarResponsable">Añadir responsable</v-btn>
     <v-btn @click="abrirDialogoEliminarResponsable">Borrar responsable</v-btn>
     <v-btn @click="abrirDialogoActualizarResponsable">Actualizar responsable</v-btn>
   </v-container>
 
   <v-dialog v-model="dialogAgregarResponsable" max-width="500">
     <v-card>
       <v-card-title>Añadir Nuevo Responsable</v-card-title>
       <v-card-text>
         <v-text-field v-model="nuevoResponsable.id" label="ID"></v-text-field>
         <v-text-field v-model="nuevoResponsable.numeroEmpleado" label="Numero de empleado del responsable"></v-text-field>
         <v-text-field v-model="nuevoResponsable.nombre" label="Nombre del responsable"></v-text-field>
         <v-text-field v-model="nuevoResponsable.activosCustodiados" label="Activos custodiados"></v-text-field>
         <v-text-field v-model="nuevoResponsable.imagen" label="Imagen"></v-text-field>
       </v-card-text>
       <v-card-actions>
         <v-btn color="primary" @click="agregarResponsable">Agregar</v-btn>
         <v-btn color="error" @click="cerrarDialogoAgregarResponsable">Cancelar</v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog>
 
   <v-dialog v-model="dialogBorrarResponsable" max-width="500">
     <v-card>
       <v-card-title>Eliminar Responsable Por ID</v-card-title>
       <v-card-text>
         <v-text-field v-model="responsableABorrar.id" label="ID"></v-text-field>
       </v-card-text>
       <v-card-actions>
         <v-btn color="primary" @click="eliminarResponsable(responsableABorrar.id)">Eliminar</v-btn>
         <v-btn color="error" @click="cerrarDialogoEliminarResponsable">Cancelar</v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog>
 
   <v-dialog v-model="dialogActualizarResponsable" max-width="500">
     <v-card>
       <v-card-title>Actualizar Responsable Por ID</v-card-title>
       <v-card-text>
        <v-text-field v-model="responsableAActualizar.id" label="ID"></v-text-field>
        <v-text-field v-model="responsableAActualizar.numeroEmpleado" label="Numero de empleado del responsable"></v-text-field>
        <v-text-field v-model="responsableAActualizar.nombre" label="Nombre del responsable"></v-text-field>
        <v-text-field v-model="responsableAActualizar.activosCustodiados" label="Activos custodiados"></v-text-field>
        <v-text-field v-model="responsableAActualizar.imagen" label="Imagen"></v-text-field>
        </v-card-text>
       <v-card-actions>
         <v-btn color="primary" @click="actualizarResponsable(responsableAActualizar.id)">Actualizar</v-btn>
         <v-btn color="error" @click="cerrarDialogoActualizarResponsable">Cancelar</v-btn>
       </v-card-actions>
     </v-card>
   </v-dialog> 
   </template>
   
   <script>
   
   export default {
     name: 'obtenerResponsable',
     data() {
       return {
         search: '',
         dataItems: [],
         headers: [
           { text: 'ID', value: 'id'},
           { text: 'Numero empleado', value: 'numeroEmpleado'},
           { text: 'Nombre', value: 'nombre'},
           { text: 'Activos custodiados', value: 'activosCustodiados', align: 'left', sortable: false },
           { text: 'Imagen', value: 'imagen'},
         ],
         dialogAgregarResponsable: false,
         dialogBorrarResponsable: false,
         dialogActualizarResponsable: false,
         nuevoResponsable: {
           id: '',
           numeroEmpleado: '',
           nombre: '',
           activosCustodiados: '',
           imagen: ''
         },
         responsableABorrar: {
           id: ''
         },
         responsableAActualizar: {
           id: '',
           numeroEmpleado: '',
           nombre: '',
           activosCustodiados: '',
           imagen: ''
         }
       };
     },
     mounted() {
       this.getResponsables();
     },
     methods: {
   async getResponsables() {
   try {
     const response = await fetch('https://localhost:4000/responsables', {
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
   async eliminarResponsable(id) {
     try {
       const response = await fetch(`https://localhost:4000/responsables/eliminarPorId/${id}`, {
         method: 'DELETE',
         mode: 'cors',
         credentials: 'same-origin',
       });
       this.getResponsables();
       if(!response.ok){
         throw new Error('Error al borrar al responsable');
       }
     } catch (error) {
       console.error('Error fetching data:', error);
     }
   },
   async agregarResponsable() {
     try {
       const response = await fetch(`https://localhost:4000/responsables`, {
         method: 'POST',
         mode: 'cors',
         headers: {
         'Content-Type': 'application/json' // Indica que estás enviando datos en formato JSON
         },
         body: JSON.stringify({
           id: this.nuevoResponsable.id,
           numeroEmpleado: this.nuevoResponsable.numeroEmpleado,
           nombre: this.nuevoResponsable.nombre,
           activosCustodiados: this.nuevoResponsable.activosCustodiados,
           imagen: this.nuevoResponsable.imagen
         }),
         credentials: 'same-origin',
       });
       this.getResponsables();
       if(response.status==400){
         throw new Error('Error al agregar al responsable, ese ID ya existe');
       }
       console.log(response.status);
     } catch (error) {
       console.error('Error fetching data:', error);
     }
   },
   async actualizarResponsable(id) {
     try {
       const existe = await fetch(`https://localhost:4000/responsables/buscarPorId/${id}`, {
         method: 'GET',
         mode: 'cors',
         credentials: 'same-origin'
       });
       console.log(existe);
       if(existe.status==404){
         throw new Error('Error al verificar la existencia del responsable');
       }
 
       const response = await fetch(`https://localhost:4000/responsables/actualizarPorId/${id}`, {
         method: 'PUT',
         mode: 'cors',
         headers: {
         'Content-Type': 'application/json' // Indica que estás enviando datos en formato JSON
         },
         body: JSON.stringify({
           numeroEmpleado: this.responsableAActualizar.numeroEmpleado,
           nombre: this.responsableAActualizar.nombre,
           activosCustodiados: this.responsableAActualizar.activosCustodiados,
           imagen: this.responsableAActualizar.imagen
         }),
         credentials: 'same-origin',
       });
       console.log(response.body);
       this.getResponsables();
       console.log(response.status);
       if(response.status==404){
         throw new Error('Error al actualizar el responsable, ese ID no existe');
       }
     } catch (error) {
       console.error('Error fetching data:', error);
     }
   },
     abrirDialogoAgregarResponsable() {
       this.dialogAgregarResponsable = true;
     },
     cerrarDialogoAgregarResponsable() {
       this.dialogAgregarResponsable = false;
     },
     abrirDialogoEliminarResponsable() {
       this.dialogBorrarResponsable = true;
     },
     cerrarDialogoEliminarResponsable() {
       this.dialogBorrarResponsable = false;
     },
     abrirDialogoActualizarResponsable() {
       this.dialogActualizarResponsable = true;
     },
     cerrarDialogoActualizarResponsable() {
       this.dialogActualizarResponsable = false;
     }
 },
   };
   </script>