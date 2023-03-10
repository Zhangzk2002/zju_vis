<script>
import * as d3 from 'd3'
import { eventBus } from '../main'

export default {
  props: {
    data: { type: Object, required: true },
    radius: { type: Number, required: true }
  },
  data() {
    return {
      format: d3.format(',')
    }
  },
  computed: {
    descendants() {
      return this.data.descendants().filter(d => d.depth)
    },
    labels() {
      return this.descendants.filter(d => d.depth && (d.y0 + d.y1) / 2 * (d.x1 - d.x0) > 50)
    },
    colors() {
      return d3.quantize(d3.interpolateSpectral, this.data.children.length)
    },
    colorKey() {
      const regions = this.descendants.filter(d => d.depth === 1)

      let i = 0
      let colorKey = new Map
      for (const region of regions) {
        colorKey.set(region.data.key, this.colors[i])
        i++
      }
      return colorKey
    }
  },
  methods: {
    openTooltip(h, event) {
      const label = 'Population: '
      const item = {
        parent: h.parent.data.key,
        current: h.data.country || h.data.key,
        value: h.value
      }
      eventBus.$emit('openTooltip', { item, event, label })
    },
    closeTooltip(event) {
      eventBus.$emit('closeTooltip', { event })
    },
    mouseOverChildren(item, event) {
      this.openTooltip(item, event)
      event.target.style.fill = 'rgba(0, 0, 0, .25)'
      event.target.style.stroke = 'rgba(255, 255, 255, .5)'
    },
    mouseOutChildren(event) {
      this.closeTooltip(event)
      event.target.style.fill = 'rgba(0, 0, 0, 0)'
      event.target.style.stroke = 'rgba(255, 255, 255, .25)'
    },
    // Starburst chart is multiple donut chart: https://github.com/d3/d3-shape/blob/v1.3.7/README.md#arcs
    arc(d) {
      const arc = d3
        .arc()
        .innerRadius(d.y0)
        .outerRadius(d.y1 - 1)
        .startAngle(d.x0)
        .endAngle(d.x1)
        .padAngle(Math.min((d.x1 - d.x0) / 2, 0.005))
        .padRadius(this.radius / 2)

      return arc()
    },
    color(d) {
      if (d.depth === 2) {
        d = d.parent
      }
      if (d.depth === 3) {
        d = d.parent.parent
      }
      const region = d.data.key
      return this.colorKey.get(region)
    },
    transform(d) {
      const x = (((d.x0 + d.x1) / 2) * 180) / Math.PI
      const y = (d.y0 + d.y1) / 2
      return `transform: rotate(${x - 90}deg) translate(${y}px,0px) rotate(${x < 180 ? 0 : 180}deg);`
    }
  }
}
</script>

<template>
  <div>
    <!-- <h2>Sunburst (Hover to highlight each region)</h2> -->
    <svg
      :viewBox="`-${radius}, -${radius} ${radius * 2} ${radius * 2}`"
      :width="radius * 2"
      :height="radius * 2"
    >
      <g>
        <path
          v-for="(item, i) in descendants"
          :key="i"
          :d="arc(item)"
          :fill="color(item)"
          :class="$style.item"
          @mouseover="openTooltip(item, $event)"
          @mouseout="closeTooltip($event)"
        />
      </g>
      <g>
        <text
          v-for="(item, i) in labels"
          :key="i"
          :style="transform(item)"
          :class="$style.label"
        >
          {{ item.data.key }}
        </text>
      </g>
    </svg>
  </div>
</template>

<style module>
.item {
  opacity: 0.75;
}
.label {
  fill: rgba(0, 0, 0, 0.75);
  font-size: 21px;
  pointer-events: none;
  text-anchor: middle;
}
</style>
