<template>
  <a-row>
    <br />
    <a-col :xs="2" :sm="4" :md="6" :lg="8" :xl="10"></a-col>
    <a-col :xs="20" :sm="16" :md="12" :lg="8" :xl="4">
      <div class="search-wrapper">
        <input type="text" v-model="nombrePais" placeholder="Escriba el nombre de un país" />
        <button @click="buscar">Buscar</button>
      </div>
      <a-table :dataSource="paises" :columns="columnas">
        <template #bandera="{ text }">
          <img :src="text" alt="Bandera" class="flag-img" />
        </template>
        <template #accion="{ text }">
          <div>
            <a-button type="primary" @click="mostrarModal(text)">Detalles</a-button>
            <a-modal v-model:visible="visible" title="Detalles del país" @ok="aceptarModal">
              <div v-if="paisSeleccionado">
                <h4>Bandera:</h4>
                <img :src="paisSeleccionado.bandera" class="flag-img" />
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
    <a-col :xs="2" :sm="4" :md="6" :lg="8" :xl="10">
      <div id="result" v-html="detallesPais"></div>
    </a-col>
  </a-row>
</template>

<style>
/* Estilos adicionales */

.search-wrapper {
  display: flex;
  align-items: center;
  margin-bottom: 16px;
}

.flag-img {
  width: 20px;
  height: auto;
}

@media (max-width: 576px) {
  .search-wrapper {
    flex-direction: column;
  }
}
</style>
