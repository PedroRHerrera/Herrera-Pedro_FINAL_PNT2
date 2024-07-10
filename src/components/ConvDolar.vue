<template>
  <div class="alert alert-info" style="text-align: center;">
    <strong>Hola!</strong> El autor de esta web es <strong><u>Pedro Ricardo Herrera</u></strong>, del curso YA-E.
  </div>
  
  <h1><u>Conversor de Pesos a Dólares</u></h1>

  <br>

  <div>
    <label for="pesos"><h5>Ingrese monto en Pesos:</h5></label>
    <input v-model.number="pesos" id="pesos" type="number" />
  </div>

  <br>

  <div>
    <label for="cotizacion"><h5>Cotización del Dólar:</h5></label>
    <input v-model.number="cotizacion" id="cotizacion" type="number" />
  </div>

  <br>

  <div class="form-check form-switch">
    <label for="autoUpdate"><h5>Actualización Automática</h5></label>
    <input v-model="autoUpdate" id="autoUpdate" type="checkbox" class="form-check-input" />
  </div>

  <br>

  <div v-if="autoUpdate">
    <p style="color:red;">Última actualización: {{ lastUpdated }}</p>
  </div>

  <h2>Resultado: {{ resultado }} USD</h2>

</template>

<script lang="js">

import axios from 'axios';

export default {
  name: 'src-componentes-dolar-peso',
  props: [],
  data() {
    return {
      pesos: 0,
      cotizacion: 0,
      resultado: 0,
      autoUpdate: false,
      lastUpdated: ''
    };
  },
  mounted() {
    this.fetchCotizacion();
  },
  beforeUnMount() {
    this.stopAutoUpdate();
  },
  watch: {
    pesos() {
      this.convertir();
    },
    cotizacion() {
      this.convertir();
    },
    autoUpdate(newVal) {
      if (newVal) {
        this.startAutoUpdate();
      } else {
        this.stopAutoUpdate();
      }
    }
  },
  methods: {
    convertir() {
      this.resultado = (this.pesos / this.cotizacion).toFixed(2);
    },
    async fetchCotizacion() {
      try {
        const response = await axios.get('https://api.bluelytics.com.ar/v2/latest');
        this.cotizacion = response.data.blue.value_sell;
        this.lastUpdated = new Date().toLocaleString();
      } catch (error) {
        console.error('Error obteniendo la cotizacion actual:', error);
      }
    },
    startAutoUpdate() {
      this.fetchCotizacion();
      this.interval = setInterval(this.fetchCotizacion, 2000);
    },
    stopAutoUpdate() {
      clearInterval(this.interval);
    }
  }
};

</script>

<style scoped lang="css">

h1 {
  text-align: center;
}

h2 {
  color: lightgreen;
}


</style>
