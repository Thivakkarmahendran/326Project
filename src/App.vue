<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Enrollment/>
  </div>
</template>

<script>
import Enrollment from './components/Enrollment.vue'

export default {
  name: 'app',
  components: {
    Enrollment
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
  margin-top: 60px;
}
</style>
