<template>
  <div class="sparkline-group" :id="`sparkline-${id}-${variable}`">
    <svg :width="width" :height="height" class="epi-sparkline" ref="svg"></svg>
  </div>
</template>

<script lang="js">
import Vue from "vue";

import { select, selectAll, scaleLinear, scaleTime, extent, max, area } from "d3";

export default Vue.extend({
  name: "Sparkline",
  props: {
    data: Array,
    width: Number,
    height: Number,
    variable: String,
    id: String,
    color: String
  },
  data() {
    return {
      // axes
      y: scaleLinear(),
      x: scaleTime(),
      // refs
      chart: null,
      // methods
      area: null
    };
  },
  watch: {
    data: function() {
      this.updatePlot();
    }
  },
  methods: {
    setupPlot() {
      this.svg = select(`#sparkline-${this.id}-${this.variable}`)
        .select("svg.epi-sparkline");
      this.chart = this.svg.select("#case-counts");

      this.chart = this.svg.append("g").attr("class", "sparkline");

      this.area = area()
        .x(d => this.x(d.date))
        .y0(d => this.y(0))
        .y1(d => this.y(d[this.variable]));
    },
    updatePlot() {
      if (this.data && this.data[0] && this.width && this.height) {
        this.updateScales();
        this.drawPlot();
      }
    },
    updateScales() {
      this.x = this.x
        .range([0, this.width])
        .domain(extent(this.data[0], d => d.date));

      this.y = this.y
        .range([this.height, 0])
        .domain([0, max(this.data[0], d => d[this.variable])]);
    },
    drawPlot() {
      const sparkSelector = this.chart.selectAll(".sparkline").data(this.data);

      const sparkEnter = sparkSelector
        .enter()
        .append("path")
        .attr("class", "sparkline");

      // merge
      sparkSelector
        .merge(sparkEnter)
        .datum(d => d)
        .join("path")
        .style("fill", this.color)
        .attr("d", this.area);

      const curtainSelector = this.chart
        .selectAll(".curtainline")
        .data(this.data);

      const curtainEnter = curtainSelector
        .enter()
        .append("path")
        .attr("class", "curtainline");

      // merge
      curtainSelector
        .merge(curtainEnter)
        .datum(d => d)
        .join("path")
        .style("fill", this.color)
        .attr("d", this.area);
    }
  },
  mounted() {
    this.setupPlot();
    this.updatePlot();
  }
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss"></style>
