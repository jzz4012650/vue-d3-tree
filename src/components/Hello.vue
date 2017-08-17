<template>
  <svg ref="svg">
    <g ref="wrapper" :transform="transform">
      <g class="links">
        <path v-for="d in treeLinks" :key="d.id" :d="diagonal(d.source, d.target)" pointer-events="none"></path>
      </g>
      <g class="nodes">
        <circle v-for="d in treeNodes" :key="d.id" :cx="d.y" :cy="d.x"></circle>
      </g>
    </g>
  </svg>
</template>

<script>
import * as d3 from 'd3'

const diagonal = (s, d) => {
  const path = `M ${s.y} ${s.x} C ${(s.y + d.y) / 2} ${s.x}, ${(s.y + d.y) / 2} ${d.x}, ${d.y} ${d.x}`
  return path
}

export default {

  props: {
    nodes: Object
  },

  data() {
    return {
      diagonal,
      w: 400,
      h: 400,
      transform: '',
      treeLinks: [],
      treeNodes: [],
      msg: 'Welcome to Your Vue.js App'
    }
  },

  watch: {
    nodes: (val) => {
      this.generate(val)
    }
  },

  methods: {
    generate(nodes) {
      const nodesCopy = JSON.parse(JSON.stringify(nodes))
      const rootNode = d3.hierarchy(nodesCopy)
      const treeNodes = []
      let index = 0
      d3.tree().size([this.w, this.h])(rootNode)
      rootNode.eachBefore(d => { d.id = ++index; treeNodes.push(d) })
      this.treeLinks = rootNode.links()
      this.treeNodes = treeNodes
    },
    handleZoom() {
      this.transform = d3.event.transform
    }
  },

  mounted() {
    const zoom = d3.zoom().on('zoom', this.handleZoom)
    this.w = this.$refs['svg'].clientWidth
    this.h = this.$refs['svg'].clientHeight
    this.generate(this.nodes)
    zoom(d3.select(this.$el))
    console.log(this.treeNodes)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
 <style scoped>
.links path {
  stroke: #ccc;
  stroke-width: 1px;
  fill: none;
}

.nodes circle {
  r: 5px;
  fill: grey;
}
</style>
