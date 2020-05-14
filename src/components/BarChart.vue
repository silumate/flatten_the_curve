<template>
<div class="BarChart">
  <svg :width="outsideWidth"
       :height="outsideHeight">
    <g :transform="`translate(${margin.left},${margin.top})`">
      <g class="bars">
        <template v-for="item in items">
          <rect class="bar"
                :key="item.id"
                :x="scale.x(item.name)"
                :y="scale.y(item.val)"
                :width="scale.x.bandwidth()"
                :height="height - scale.y(item.val)"
                />
        </template>
        <g ref="axisX" :transform="`translate(0,${height})`"></g>
        <g ref="axisY"></g>
      </g>
    </g>
  </svg>
</div>
</template>

<script>
import * as d3 from "d3";
export default {
  name: 'BarChart',
  data() {
    return {
      width: 600,
      height: 400,
      margin: {
        top: 20,
        right: 20,
        bottom: 20,
        left: 20,
      },
      items: [
        { name: 'a', val: 10 },
        { name: 'b', val:  8 },
        { name: 'c', val:  1 },
        { name: 'd', val:  5 },
        { name: 'e', val:  6 },
        { name: 'f', val:  3 },        
      ],
    };
  },
  mounted() {
    this.addAxisX();
    this.addAxisY();
  },
  computed: {
    outsideWidth() {
      return this.width + this.margin.left + this.margin.right;
    },
    outsideHeight() {
      return this.height + this.margin.top + this.margin.bottom;
    },
    scale() {
      const x = d3.scaleBand()
        .domain(this.items.map(x => x.name))
        .rangeRound([0, this.width]).padding(0.15);
      const y = d3.scaleLinear()
        .domain([0, Math.max(...this.items.map(x => x.val))])
        .rangeRound([this.height, 0]);
      return {x, y};
    }
  },
  watch: {
    scale() {
      this.addAxisX();
      this.addAxisY();
    }
  },
  methods: {
    addAxisX() {
      d3.select(this.$refs.axisX)
        .call(d3.axisBottom(this.scale.x));
    },
    addAxisY() {
      d3.select(this.$refs.axisY)
        .call(d3.axisLeft(this.scale.y));
    }
  }  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
rect.bar {
  fill: steelblue;
}
</style>
