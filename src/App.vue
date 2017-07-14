<template>
  <div id="app">
    <div class="logo__block">
      <img class="logo" src="./assets/logo.png">
      Made by Egor Madiarov
    </div>
    <hello></hello>
  </div>
</template>

<script>
import Hello from './components/Hello'
import './assets/css/app.css'
import './assets/css/foundation.min.css'

export default {
  name: 'app',
  components: {
    Hello
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

.logo__block {
  margin: 5px 10px;
  text-align: right;
}

.logo {
  height: 25px;
}
</style>
