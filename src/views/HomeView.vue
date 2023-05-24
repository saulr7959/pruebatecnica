<template>
  <a-row type="flex">
      <br>
      <a-col :span="3"></a-col>
      <a-col :span="12">
          <div class="search-wrapper">
              <input type="text" v-model="nombrePais" placeholder="Escriba el nombre de un país" />
              <button @click="buscar">Buscar</button>
          </div>
          <a-table :dataSource="paises" :columns="columnas">
              <template #bandera="{ text }">
                  <img :src="text" alt="Bandera" class="flag-img" style="width: 20px; height: auto;" />
              </template>
              <template #accion="{ text }">
                  <div>
                      <a-button type="primary" @click="mostrarModal(text)">Detalles</a-button>
                      <a-modal v-model:visible="visible" title="Detalles del país" @ok="aceptarModal">
                          <div v-if="paisSeleccionado">
                              <h4>Bandera:</h4>
                              <img :src="paisSeleccionado.bandera" class="flag-img" style="width: 100px; height: auto;">
                              <h1>Información:</h1>
                              <h2>{{ paisSeleccionado.nombre }}</h2>
                              <h4>Capital:</h4>
                              <span>{{ paisSeleccionado.capital }}</span>

                              <h4>Población:</h4>
                              <span>{{ paisSeleccionado.poblacion }}</span>
                          </div>
                      </a-modal>
                  </div>
              </template>
          </a-table>
      </a-col>
      <a-col :span="9">
          <div id="result" v-html="detallesPais"></div>
      </a-col>
  </a-row>
</template>

<script>
// imports
import axios from 'axios';
import { defineComponent, ref } from 'vue';

export default {
  setup() {
      const visible = ref(false);
      const paisSeleccionado = ref(null);

      const mostrarModal = (pais) => {
          paisSeleccionado.value = pais;
          console.log(paisSeleccionado)
          visible.value = true;
      };

      const aceptarModal = e => {
          console.log(e);
          visible.value = false;
      };

      return {
          visible,
          paisSeleccionado,
          mostrarModal,
          aceptarModal,
      };
  },

  data() {
      return {
          paises: null,
          columnas: [
              {
                  title: 'Banderas',
                  dataIndex: 'bandera',
                  key: 'bandera',
                  slots: { customRender: 'bandera' },
              },
              {
                  title: 'País',
                  dataIndex: 'nombre',
                  key: 'nombre',
              },
              {
                  title: 'Población',
                  dataIndex: 'poblacion',
                  key: 'poblacion',
              },
              {
                  title: 'Acción',
                  dataIndex: 'accion',
                  key: 'accion',
                  slots: { customRender: 'accion' },
              },
          ],
          nombrePais: '',
          detallesPais: '',
      };
  },

  mounted() {
      this.obtenerPaises();
  },

  methods: {
      obtenerPaises() {
          axios
              .get('https://restcountries.com/v3.1/all')
              .then((response) => {
                  this.paises = response.data.map((pais, index) => ({
                      key: index.toString(),
                      bandera: pais.flags.svg,
                      nombre: pais.name.common,
                      poblacion: pais.population,
                  }));
              })
              .catch((error) => {
                  console.error('Error al obtener los países:', error);
              });
      },

      buscar() {
          if (this.nombrePais.length === 0) {
              this.detallesPais = '<h3>POR FAVOR, COMPLETE LA INFORMACIÓN</h3>';
              return;
          }

          const finalURL = `https://restcountries.com/v3.1/name/${this.nombrePais}?fullText=true`;
          axios
              .get(finalURL)
              .then((response) => {
                  const data = response.data[0];
                  this.detallesPais = `
          <h4>Bandera:</h4>
            <img src="${data.flags.svg}" class="flag-img" style="width: 100px; height: auto;">
            <h1>Información:</h1>
            <h2>${data.name.common}</h2>
            <div class="wrapper">
              <div class="data-wrapper">
                <h4>Capital:</h4>
                <span>${data.population}</span>
              </div>
            </div>
            <div class="wrapper">
              <div class="data-wrapper">
                <h4>Código numérico:</h4>
                <span>${data.ccn3}</span>
              </div>
            </div>
            <div class="wrapper">
              <div class="data-wrapper">
                <h4>Código Alpha2:</h4>
                <span>${data.tld}</span>
              </div>
            </div>
          `;
              })
              .catch(() => {
                  this.detallesPais = '<h3>POR FAVOR INGRESE UN PAÍS VÁLIDO.</h3>';
              });
      },
  },
};
</script>

<style>
/* Estilos adicionales */
</style>

