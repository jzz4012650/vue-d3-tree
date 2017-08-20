<template>
  <div id="app">
    <tree-diagram style="display: block; width: 800px; height: 600px;" :nodes="data" />
  </div>
</template>

<script>
import data from './assets/test.json'
import TreeDiagram from './components/TreeDiagram'

export default {

  components: {
    TreeDiagram
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
