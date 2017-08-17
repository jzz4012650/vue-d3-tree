<template>
  <div id="app">
    <hello style="display: block; width: 800px; height: 600px;" :nodes="data"></hello>
  </div>
</template>

<script>
import data from './assets/test.json'
import Hello from './components/Hello'

export default {

  components: {
    Hello
  },

  data: function () {
    return {
      data
    }
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
