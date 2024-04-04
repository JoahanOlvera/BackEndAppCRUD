<template>
   <v-card flat title="Datos de los activos">
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

    <v-data-table :headers="headers" :items="dataItems" :search="search"></v-data-table>
  </v-card>

  <v-container class="d-flex align-center justify-center">
    <v-btn @click="abrirDialogoAgregarActivo">Añadir activo</v-btn>
    <v-btn @click="abrirDialogoEliminarActivo">Borrar activo</v-btn>
    <v-btn @click="abrirDialogoActualizarActivo">Actualizar activo</v-btn>
  </v-container>

  <v-dialog v-model="dialogAgregarActivo" max-width="500">
    <v-card>
      <v-card-title>Añadir Nuevo Activo</v-card-title>
      <v-card-text>
        <v-text-field v-model="nuevoActivo.id" label="ID"></v-text-field>
        <v-text-field v-model="nuevoActivo.numeroSerie" label="Numero de serie"></v-text-field>
        <v-text-field v-model="nuevoActivo.numeroInventario" label="Numero de inventario"></v-text-field>
        <v-text-field v-model="nuevoActivo.tipo" label="Tipo"></v-text-field>
        <v-text-field v-model="nuevoActivo.descripcion" label="Descripcion"></v-text-field>
        <v-text-field v-model="nuevoActivo.ubicacionId" label="Id de ubicación"></v-text-field>
        <v-text-field v-model="nuevoActivo.responsableId" label="Id de responsable"></v-text-field>
        <v-text-field v-model="nuevoActivo.imagen" label="Imagen"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="agregarActivo">Agregar</v-btn>
        <v-btn color="error" @click="cerrarDialogoAgregarActivo">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="dialogBorrarActivo" max-width="500">
    <v-card>
      <v-card-title>Eliminar Activo Por ID</v-card-title>
      <v-card-text>
        <v-text-field v-model="activoABorrar.id" label="ID"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="eliminarActivo(activoABorrar.id)">Eliminar</v-btn>
        <v-btn color="error" @click="cerrarDialogoEliminarActivo">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="dialogActualizarActivo" max-width="500">
    <v-card>
      <v-card-title>Actualizar Activo Por ID</v-card-title>
      <v-card-text>
        <v-text-field v-model="activoAActualizar.id" label="ID"></v-text-field>
        <v-text-field v-model="activoAActualizar.numeroSerie" label="Numero de serie"></v-text-field>
        <v-text-field v-model="activoAActualizar.numeroInventario" label="Numero de inventario"></v-text-field>
        <v-text-field v-model="activoAActualizar.tipo" label="Tipo"></v-text-field>
        <v-text-field v-model="activoAActualizar.descripcion" label="Descripcion"></v-text-field>
        <v-text-field v-model="activoAActualizar.ubicacionId" label="Id de ubicación"></v-text-field>
        <v-text-field v-model="activoAActualizar.responsableId" label="Id de responsable"></v-text-field>
        <v-text-field v-model="activoAActualizar.imagen" label="Imagen"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="actualizarActivo(activoAActualizar.id)">Actualizar</v-btn>
        <v-btn color="error" @click="cerrarDialogoActualizarActivo">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog> 
  </template>
  
  <script>
  
  export default {
    name: 'obtenerActivos',
    data() {
      return {
        search: '',
        dataItems: [],
        headers: [
          { text: 'ID', value: 'id'},
          { text: 'Numero de serie', value: 'numeroSerie'},
          { text: 'Numero de inventario', value: 'numeroInventario'},
          { text: 'Tipo', value: 'tipo'},
          { text: 'Descripcion', value: 'descripcion'},
          { text: 'Id de ubicacion', value: 'ubicacionId'},
          { text: 'Id de responsable', value: 'responsableId'},
          { text: 'Imagen', value: 'imagen'},
        ],
        dialogAgregarActivo: false,
        dialogBorrarActivo: false,
        dialogActualizarActivo: false,
        nuevoActivo: {
          id: '',
          numeroSerie: '',
          numeroInventario: '',
          tipo: '',
          descripcion: '',
          ubicacionId: '',
          responsableId: '',
          imagen: ''
        },
        activoABorrar: {
          id: ''
        },
        activoAActualizar: {
          id: '',
          numeroSerie: '',
          numeroInventario: '',
          tipo: '',
          descripcion: '',
          ubicacionId: '',
          responsableId: '',
          imagen: ''
        }
      };
    },
    mounted() {
      this.getActivos();
    },
    methods: {
  async getActivos() {
  try {
    const response = await fetch('https://localhost:4000/activos', {
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
  async eliminarActivo(id) {
    try {
      const response = await fetch(`https://localhost:4000/activos/eliminarPorId/${id}`, {
        method: 'DELETE',
        mode: 'cors',
        credentials: 'same-origin',
      });
      this.getActivos();
      if(!response.ok){
        throw new Error('Error al borrar el activo');
      }
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  },
  async agregarActivo() {
    try {
      const response = await fetch(`https://localhost:4000/activos`, {
        method: 'POST',
        mode: 'cors',
        headers: {
        'Content-Type': 'application/json' // Indica que estás enviando datos en formato JSON
        },
        body: JSON.stringify({
          id: this.nuevoActivo.id,
          numeroSerie: this.nuevoActivo.numeroSerie,
          numeroInventario: this.nuevoActivo.numeroInventario,
          tipo: this.nuevoActivo.tipo,
          descripcion: this.nuevoActivo.descripcion,
          ubicacionId: this.nuevoActivo.ubicacionId,
          responsableId: this.nuevoActivo.responsableId,
          imagen: this.nuevoActivo.imagen
        }),
        credentials: 'same-origin',
      });
      this.getActivos();
      if(response.status==400){
        throw new Error('Error al agregar el activo, ese ID ya existe');
      }
      console.log(response.status);
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  },
  async actualizarActivo(id) {
    try {
      const existe = await fetch(`https://localhost:4000/activos/buscarPorId/${id}`, {
        method: 'GET',
        mode: 'cors',
        credentials: 'same-origin'
      });

      if(existe.status==404){
        throw new Error('Error al verificar la existencia del activo');
      }

      const response = await fetch(`https://localhost:4000/activos/actualizarPorId/${id}`, {
        method: 'PUT',
        mode: 'cors',
        headers: {
        'Content-Type': 'application/json' // Indica que estás enviando datos en formato JSON
        },
        body: JSON.stringify({
          numeroSerie: this.activoAActualizar.numeroSerie,
          numeroInventario: this.activoAActualizar.numeroInventario,
          tipo: this.activoAActualizar.tipo,
          descripcion: this.activoAActualizar.descripcion,
          ubicacionId: this.activoAActualizar.ubicacionId,
          responsableId: this.activoAActualizar.responsableId,
          imagen: this.activoAActualizar.imagen
        }),
        credentials: 'same-origin',
      });
      console.log(response.body);
      this.getActivos();
      console.log(response.status);
      if(response.status==404){
        throw new Error('Error al actualizar el activo, ese ID no existe');
      }
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  },
    abrirDialogoAgregarActivo() {
      this.dialogAgregarActivo = true;
    },
    cerrarDialogoAgregarActivo() {
      this.dialogAgregarActivo = false;
    },
    abrirDialogoEliminarActivo() {
      this.dialogBorrarActivo = true;
    },
    cerrarDialogoEliminarActivo() {
      this.dialogBorrarActivo = false;
    },
    abrirDialogoActualizarActivo() {
      this.dialogActualizarActivo = true;
    },
    cerrarDialogoActualizarActivo() {
      this.dialogActualizarActivo = false;
    }
},
  };
  </script>