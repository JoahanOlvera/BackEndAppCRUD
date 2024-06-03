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
      <template v-slot:item.imagenBase64="{ item }">
        <v-img :src="'data:image/png;base64,' + item.imagenBase64" max-width="100" max-height="100"></v-img>
      </template>
    </v-data-table>
  </v-card>

  <v-container class="d-flex align-center justify-center">
    <v-btn @click="abrirDialogoAgregarUbicacion">Añadir ubicación</v-btn>
    <v-btn @click="abrirDialogoEliminarUbicacion">Borrar ubicación</v-btn>
    <v-btn @click="abrirDialogoActualizarUbicacion">Actualizar ubicación</v-btn>
    <v-btn @click="getUbicaciones">Recargar ubicaciones</v-btn> <!-- Botón para recargar ubicaciones -->
  </v-container>

  <v-dialog v-model="dialogAgregarUbicacion" max-width="500">
    <v-card>
      <v-card-title>Añadir Nueva Ubicación</v-card-title>
      <v-card-text>
        <v-text-field v-model="nuevaUbicacion.id" label="ID"></v-text-field>
        <v-text-field v-model="nuevaUbicacion.descripcion" label="Descripción de la ubicación"></v-text-field>
        <v-file-input
          v-model="nuevaUbicacion.imagen"
          label="Imagen"
          accept="image/*"
          prepend-icon="mdi-camera"
          @change="handleImageUpload"
        ></v-file-input>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="agregarUbicacion">Agregar</v-btn>
        <v-btn color="error" @click="cerrarDialogoAgregarUbicacion">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="dialogBorrarUbicacion" max-width="500">
    <v-card>
      <v-card-title>Eliminar Ubicación Por ID</v-card-title>
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
      <v-card-title>Actualizar Ubicación Por ID</v-card-title>
      <v-card-text>
        <v-text-field v-model="ubicacionAActualizar.id" label="ID"></v-text-field>
        <v-text-field v-model="ubicacionAActualizar.descripcion" label="Descripción"></v-text-field>
        <v-file-input
          v-model="ubicacionAActualizar.imagen"
          label="Imagen"
          accept="image/*"
          prepend-icon="mdi-camera"
          @change="handleImageUpload"
        ></v-file-input>
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
        { text: 'ID', value: 'id' },
        { text: 'Descripción', value: 'descripcion' },
        { text: 'Activos', value: 'activos', align: 'left', sortable: false },
        { text: 'Imagen', value: 'imagenBase64' }, // Asegúrate de que el valor coincide con el campo en el backend
      ],
      dialogAgregarUbicacion: false,
      dialogBorrarUbicacion: false,
      dialogActualizarUbicacion: false,
      nuevaUbicacion: {
        id: '',
        descripcion: '',
        imagenBase64: '' // Asegúrate de que esta propiedad está definida
      },
      ubicacionABorrar: {
        id: ''
      },
      ubicacionAActualizar: {
        id: '',
        descripcion: '',
        imagenBase64: '' // Asegúrate de que esta propiedad está definida
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
          credentials: 'same-origin',
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
        if (!response.ok) {
          throw new Error('Error al borrar la ubicación');
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
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            id: this.nuevaUbicacion.id,
            descripcion: this.nuevaUbicacion.descripcion,
            imagenBase64: this.nuevaUbicacion.imagenBase64
          }),
          credentials: 'same-origin',
        });
        if (!response.ok) {
          throw new Error('Error al agregar la ubicación');
        }
        this.getUbicaciones();
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
        if (existe.status == 404) {
          throw new Error('Error al verificar la existencia de la ubicación');
        }

        const response = await fetch(`https://localhost:4000/ubicaciones/actualizarPorId/${id}`, {
          method: 'PUT',
          mode: 'cors',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            descripcion: this.ubicacionAActualizar.descripcion,
            imagenBase64: this.ubicacionAActualizar.imagenBase64
          }),
          credentials: 'same-origin',
        });
        if (!response.ok) {
          throw new Error('Error al actualizar la ubicación');
        }
        this.getUbicaciones();
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    handleImageUpload(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          const base64 = e.target.result.split(',')[1]; // Solo la parte base64
          if (this.dialogAgregarUbicacion) {
            this.nuevaUbicacion.imagenBase64 = base64;
          } else if (this.dialogActualizarUbicacion) {
            this.ubicacionAActualizar.imagenBase64 = base64;
          }
        };
        reader.readAsDataURL(file);
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