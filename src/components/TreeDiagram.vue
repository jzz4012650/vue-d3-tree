<template>
  <svg ref="svg">
    <g :transform="`translate(${this.padding[3]}, ${this.padding[0] + this.h / 2})`">
      <g ref="wrapper" :transform="transform">
        <g class="tree-links">
          <path class="tree-link" v-for="d in treeLinks" v-show="!onDragDescendants.find(e => e === d.target)" :key="d.id" :d="diagonal(d.source, d.target)" pointer-events="none"></path>
        </g>
        <g class="tree-nodes">
          <g class="tree-node" v-for="d in treeNodes" v-show="!onDragDescendants.find(e => e === d)" :key="d.id" :transform="`translate(${d.y},${d.x})`">
            <slot node="d"></slot>
            <circle class="tree-shadow-node" :r="nodeSize[0]" v-if="onDragNode" @mouseover="handleDragInTo(d, $event)" @mouseout="handleDragOut(d, $event)"></circle>
          </g>
          <path class="tree-shadow-link" v-if="dragTarget" :d="diagonal(dragTarget, {x: dragY, y: dragX})"></path>
          <g class="tree-draging-node" v-if="onDragNode" :transform="`translate(${dragX}, ${dragY})`" pointer-events="none">
            <slot name="dragingNode"></slot>
          </g>
        </g>
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
    nodes: Object,
    separation: Number,
    nodeSize: {
      type: Array,
      default: [10, 100]
    },
    padding: {
      type: Array,
      default: () => [20, 20, 20, 20]
    }
  },

  data() {
    return {
      diagonal,
      w: 400,
      h: 400,
      zoom: d3.zoom().on('zoom', this.handleZoom),
      drag: d3.drag().on('start', this.handleDragStart).on('drag', this.handleDrag).on('end', this.handleDragEnd),
      wrapper: null,
      transform: '',
      onDragNode: null,
      onDragDescendants: [],
      dragTarget: null,
      dragX: 0,
      dragY: 0,
      currentScale: 1
    }
  },

  computed: {
    tree: function () {
      return d3.tree().nodeSize(this.nodeSize)
    },
    rootNode: function () {
      if (this.nodes) {
        let copy = JSON.parse(JSON.stringify(this.nodes))
        return this.tree(d3.hierarchy(copy))
      }
      return null
    },
    treeNodes: function () {
      if (this.rootNode) {
        return this.rootNode.descendants()
      }
      return []
    },
    treeLinks: function () {
      if (this.rootNode) {
        return this.rootNode.links()
      }
      return []
    }
  },

  watch: {
    nodes: (val) => {
      this.initDrag()
    }
  },

  methods: {
    initDrag() {
      const treeNodes = this.wrapper.selectAll('.tree-node').data(this.treeNodes)
      treeNodes.on('click', this.handleClick)
      this.drag(treeNodes)
    },
    handleClick() {
      console.log('click')
    },
    handleDragStart(d, i) {
      this.dragY = +d3.event.x
      this.dragX = +d3.event.y
      this.onDragNode = d
      this.onDragDescendants = d.descendants()
    },
    handleDrag() {
      this.dragX = d3.event.dx + this.dragX
      this.dragY = d3.event.dy + this.dragY
    },
    handleDragEnd() {
      if (this.dragTarget) {
        this.$emit('drag', this.onDragNode, this.dragTarget)
        this.dragTarget = null
      }
      this.onDragNode = null
      this.onDragDescendants = []
    },
    handleDragInTo(node) {
      this.dragTarget = node
    },
    handleDragOut(node) {
      this.dragTarget = null
    },
    handleZoom() {
      this.transform = d3.event.transform
      this.currentScale = d3.event.transform.k
    }
  },

  mounted() {
    this.w = this.$refs['svg'].clientWidth - this.padding[1] - this.padding[3]
    this.h = this.$refs['svg'].clientHeight - this.padding[0] - this.padding[2]
    this.wrapper = d3.select(this.$el)
    this.zoom(this.wrapper)
    this.initDrag()
  },

  updated() {
  }
}
</script>

<style lang="less">
.tree-link {
  fill: none;
  stroke: #CCC;
}

.tree-shadow-node {
  fill: rgba(0, 0, 0, 0.1);
}

.tree-shadow-link {
  fill: none;
  stroke: #AAA;
  stroke-dasharray: 3;
}
</style>
