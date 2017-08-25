<template>
  <div id="app">
    <tree-diagram ref="tree" :nodes="data" node-id="id" node-pid="pid" :node-size="[30, 100]" :padding="[0, 20, 0, 20]" @drag="handleNodeDrag" style="display: block; width: 800px; height: 600px;">
      <template scope="scope">
        <circle r="5" fill="red" @click="handleNodeClick(scope.node, $event)" @contextmenu.prevent="handleNodeRightClick(scope.node, $event)"></circle>
      </template>
      <circle slot="dragingNode" r="5" fill="black"></circle>
    </tree-diagram>
  </div>
</template>

<script>
// import data from './assets/test.json'
import data from './assets/test_array.json'
import TreeDiagram from './components/TreeDiagram'

export default {

  components: {
    TreeDiagram
  },

  data() {
    return {
      data
    }
  },

  methods: {
    handleNodeClick(node, e) {
      const bound = this.$refs['tree'].$el.getBoundingClientRect()
      const position = [e.clientX - bound.left, e.clientY - bound.top]
      console.log('node-click', position)
    },
    handleNodeRightClick(node, e) {
      const bound = this.$refs['tree'].$el.getBoundingClientRect()
      const position = [e.clientX - bound.left, e.clientY - bound.top]
      console.log('node-right-click', position)
    },
    handleNodeDrag(source, target) {
      console.log(source, target)
      let _s, _t
      if (source.parent === target) {
        return
      }
      _s = this.data.find(d => d.id === source.data.id)
      _t = this.data.find(d => d.id === target.data.id)
      _s.pid = _t.id
    }
  }
}
</script>

 <style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  svg {
    background: #f3f3f3;
  }
}
</style>
