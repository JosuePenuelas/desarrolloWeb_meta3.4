<template>
    <v-card flat>
      <v-card-title>Responsables</v-card-title>
      <template v-slot:text>
        <v-text-field v-model="search" label="Buscar" prepend-inner-icon="mdi-magnify" single-line variant="outlined"
          hide-details></v-text-field>
      </template>
  
      <v-container>
        <v-btn class="mb-2" color="primary" @click="showNewDialog = true">
          New Item
        </v-btn>
        <v-btn class="mb-2" color="secondary" @click="showUpdateDialog = true">
          Update Item
        </v-btn>
        <v-btn class="mb-2" color="red" @click="showDeleteDialog = true">
          Delete Item
        </v-btn>
      </v-container>
  
      <v-data-table :headers="headers" :items="datos" :search="search" :sort-by="[{ key: 'id', order: 'asc' }]"
        :sort-by1="[{ key: 'title', order: 'asc' }]" :sort-by2="[{ key: 'body', order: 'asc' }]"></v-data-table>
  
      <!-- Diálogo para introducir ID a eliminar -->
      <v-dialog v-model="showDeleteDialog" max-width="500px">
        <v-card>
          <v-card-title>Eliminar Elemento</v-card-title>
          <v-card-text>
            <v-text-field v-model.number="idToDelete" label="ID a eliminar" type="number"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="red darken-1" text @click="eliminar(idToDelete); showDeleteDialog = false">Eliminar</v-btn>
            <v-btn text @click="showDeleteDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
      <!-- Diálogo para introducir nuevos datos -->
      <v-dialog v-model="showNewDialog" max-width="500px">
        <v-card>
          <v-card-title>Nuevo Elemento</v-card-title>
          <v-card-text>
            <v-text-field v-model.number="newId" label="ID" type="number"></v-text-field>
            <v-text-field v-model.number="newNumEmpleado" label="Número de Empleado" type="number"></v-text-field>
            <v-text-field v-model="newNombre" label="Nombre"></v-text-field>
            <v-text-field v-model="newActivosAsociados" label="Activos Asociados"@input="convertirLista"></v-text-field>
            <v-text-field v-model="newImagenResponsable" label="Imagen Responsable"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="primary" @click="crearNuevoItem">Crear</v-btn>
            <v-btn text @click="showNewDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
      <!-- Diálogo para actualizar datos -->
      <v-dialog v-model="showUpdateDialog" max-width="500px">
        <v-card>
          <v-card-title>Actualizar Elemento</v-card-title>
          <v-card-text>
            <v-text-field v-model.number="updateId" label="ID" type="number" @input="cargarDatosUpdate"></v-text-field>
            <v-text-field v-model.number="updateNumEmpleado" label="Número de Empleado" type="number"></v-text-field>
            <v-text-field v-model="updateNombre" label="Nombre"></v-text-field>
            <v-text-field v-model="updateActivosAsociados" label="ActivosAsociados"@input="convertirLista"></v-text-field>
            <v-text-field v-model="updateImagenResponsable" label="Imagen Responsable"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn color="primary" @click="actualizarItem">Actualizar</v-btn>
            <v-btn text @click="showUpdateDialog = false">Cancelar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
  
    </v-card>
  </template>

<script setup>
import { ref } from 'vue';

const search = ref('');
const headers = ref([
  { align: 'start', key: 'id', sortable: false, title: 'ID' },
  { key: 'numEmpleado', title: 'Número de serie' },
  { key: 'nombre', title: 'Número de inventario' },
  { key: 'activosAsociados', title: 'ActivosAsociados' },
  { key: 'imagenResponsable', title: 'ImagenResponsableripción' },
]);

const datos = ref([]);

//para borrar
const showDeleteDialog = ref(false);
const idToDelete = ref('');

//para nuevo dato
const showNewDialog = ref(false);
const newId = ref('');
const newNumEmpleado = ref('');
const newNombre = ref('');
const newActivosAsociados = ref('');
const newImagenResponsable = ref('');

//para nuevo dato
const showUpdateDialog = ref(false);
const updateId = ref('');
const updateNumEmpleado = ref('');
const updateNombre = ref('');
const updateActivosAsociados = ref('');
const updateImagenResponsable = ref('');

async function obtenerDatos() {
  try {
    const response = await fetch("https://localhost:4000/responsables");
    const data = await response.json();
    datos.value = data;
  } catch (error) {
    console.error('Error al obtener datos:', error);
  }
}

async function eliminar(id) {
  try {
    const response = await fetch(`https://localhost:4000/responsables/id${id}`, {
      method: 'DELETE',
    });
    obtenerDatos();

  } catch (error) {
    console.error('Error al crear el delete:', error);
  }
}

async function crearNuevoItem() {
  try {
    const response = await fetch('https://localhost:4000/responsables', {
      method: 'POST',
      body: JSON.stringify({
        id: newId.value,
        numEmpleado: newNumEmpleado.value,
        nombre: newNombre.value,
        activosAsociados: newActivosAsociados.value,
        imagenResponsable: newImagenResponsable.value,
      }),
      headers: {
        'Content-type': 'application/json; charset=UTF-8',
      },
    });
    showNewDialog.value = false;
    obtenerDatos();
  } catch (error) {
    console.error('Error al crear el nuevo post:', error);
  }
}

async function actualizarItem() {
  try {
    const response = await fetch(`https://localhost:4000/responsables/id${updateId.value}`, {
      method: 'PUT',
      body: JSON.stringify({
        id: updateId.value,
        numEmpleado: updateNumEmpleado.value,
        nombre: updateNombre.value,
        activosAsociados: updateActivosAsociados.value,
        imagenResponsable: updateImagenResponsable.value,
      }),
      headers: {
        'Content-type': 'application/json; charset=UTF-8',
      },
    });
    showUpdateDialog.value = false;
    obtenerDatos();
  } catch (error) {
    console.error('Error al crear el nuevo post:', error);
  }
}

function convertirLista(event) {
  const lista = event.target.value.split(',').map(Number);
  newActivosAsociados.value = lista;
}

async function cargarDatosUpdate() {
  try {
    const response = await fetch(`https://localhost:4000/responsables/id${updateId.value}`);
    const data = await response.json();
    updateNumEmpleado.value = data.numEmpleado;
    updateNombre.value = data.nombre;
    updateActivosAsociados.value = data.activosAsociados;
    updateImagenResponsable.value = data.imagenResponsable;
  } catch (error) {
    console.error('Error al cargar los datos para actualizar:', error);
  }
}

obtenerDatos();
</script>