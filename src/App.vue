<template>
  <div id="app">
    <vue-leaflet />
  </div>
</template>

<script>
import VueLeaflet from './components/VueLeaflet.vue'

export default {
  name: 'app',
  components: {
    VueLeaflet
  }
}
</script>

<style>
html, body, #app {
  margin: 0;
  padding: 0;
}
</style>
