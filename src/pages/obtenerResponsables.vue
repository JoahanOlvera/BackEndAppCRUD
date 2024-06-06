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
      <template v-slot:item.activos="{ item }">
        <span v-for="(activo, index) in item.activos" :key="index">
          {{ activo }}{{ index < item.activos.length - 1 ? ', ' : '' }}
        </span>
      </template>
      <template v-slot:item.imagenBase64="{ item }">
        <v-img :src="'data:image/png;base64,' + item.imagenBase64" max-width="100" max-height="100"></v-img>
      </template>
    </v-data-table>
  </v-card>

  <v-container class="d-flex align-center justify-center">
    <v-btn @click="abrirDialogoAgregarResponsable">Añadir responsable</v-btn>
    <v-btn @click="abrirDialogoEliminarResponsable">Borrar responsable</v-btn>
    <v-btn @click="abrirDialogoActualizarResponsable">Actualizar responsable</v-btn>
    <v-btn @click="getResponsables">Recargar responsables</v-btn>
  </v-container>

  <v-dialog v-model="dialogAgregarResponsable" max-width="500">
    <v-card>
      <v-card-title>Añadir Nuevo Responsable</v-card-title>
      <v-card-text>
        <v-text-field v-model="nuevoResponsable.id" label="ID"></v-text-field>
        <v-text-field v-model="nuevoResponsable.numeroEmpleado" label="Numero de empleado del responsable"></v-text-field>
        <v-text-field v-model="nuevoResponsable.nombre" label="Nombre del responsable"></v-text-field>
        <v-file-input
          v-model="nuevoResponsable.imagen"
          label="Imagen"
          accept="image/*"
          prepend-icon="mdi-camera"
          @change="handleImageUpload"
        ></v-file-input>
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
        <v-text-field v-model="responsableAActualizar.id" label="ID" @change="cargarDatosResponsable"></v-text-field>
        <v-text-field v-model="responsableAActualizar.numeroEmpleado" label="Numero de empleado del responsable"></v-text-field>
        <v-text-field v-model="responsableAActualizar.nombre" label="Nombre del responsable"></v-text-field>
        <v-file-input
          v-model="responsableAActualizar.imagen"
          label="Imagen"
          accept="image/*"
          prepend-icon="mdi-camera"
          @change="handleImageUpload"
        ></v-file-input>
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
        { text: 'ID', value: 'id' },
        { text: 'Numero empleado', value: 'numeroEmpleado' },
        { text: 'Nombre', value: 'nombre' },
        { text: 'Activos', value: 'activos' },
        { text: 'Imagen', value: 'imagenBase64' },
      ],
      dialogAgregarResponsable: false,
      dialogBorrarResponsable: false,
      dialogActualizarResponsable: false,
      nuevoResponsable: {
        id: '',
        numeroEmpleado: '',
        nombre: '',
        imagenBase64: ''
      },
      responsableABorrar: {
        id: ''
      },
      responsableAActualizar: {
        id: '',
        numeroEmpleado: '',
        nombre: '',
        imagenBase64: ''
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
    async eliminarResponsable(id) {
      try {
        const response = await fetch(`https://localhost:4000/responsables/eliminarPorId/${id}`, {
          method: 'DELETE',
          mode: 'cors',
          credentials: 'same-origin',
        });
        this.getResponsables();
        if (!response.ok) {
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
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          id: this.nuevoResponsable.id,
          numeroEmpleado: this.nuevoResponsable.numeroEmpleado,
          nombre: this.nuevoResponsable.nombre,
          imagenBase64: this.nuevoResponsable.imagenBase64
        }),
        credentials: 'same-origin',
      });
      if (!response.ok) {
        throw new Error('Error al agregar al responsable');
      }
      this.getResponsables();
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
        if (existe.status == 404) {
          throw new Error('Error al verificar la existencia del responsable');
        }

        const response = await fetch(`https://localhost:4000/responsables/actualizarPorId/${id}`, {
          method: 'PUT',
          mode: 'cors',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            numeroEmpleado: this.responsableAActualizar.numeroEmpleado,
            nombre: this.responsableAActualizar.nombre,
            imagenBase64: this.responsableAActualizar.imagenBase64
          }),
          credentials: 'same-origin',
        });
        if (!response.ok) {
          throw new Error('Error al actualizar al responsable');
        }
        this.getResponsables();
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    async cargarDatosResponsable() {
      if (!this.responsableAActualizar.id) {
        return;
      }
      try {
        const response = await fetch(`https://localhost:4000/responsables/buscarPorId/${this.responsableAActualizar.id}`, {
          method: 'GET',
          mode: 'cors',
          credentials: 'same-origin'
        });
        if (response.ok) {
          const data = await response.json();
          this.responsableAActualizar = {
            id: data.id,
            numeroEmpleado: data.numeroEmpleado,
            nombre: data.nombre,
            imagenBase64: data.imagenBase64
          };
        } else {
          throw new Error('Responsable no encontrado');
        }
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    handleImageUpload(event) {
    const file = event.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = (e) => {
        const base64 = e.target.result.split(',')[1];
        if (this.dialogAgregarResponsable) {
          this.nuevoResponsable.imagenBase64 = base64;
        } else if (this.dialogActualizarResponsable) {
          this.responsableAActualizar.imagenBase64 = base64;
        }
      };
      reader.readAsDataURL(file);
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
