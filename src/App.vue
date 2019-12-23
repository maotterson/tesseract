<template>
  <div id="app">
    <Convert/>
  </div>
</template>

<script>
import Convert from './components/Convert.vue'

export default {
  name: 'app',
  components: {
    Convert
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.primary {
  background-color: red;
}
.secondary {
  background-color: green;
}
</style>
