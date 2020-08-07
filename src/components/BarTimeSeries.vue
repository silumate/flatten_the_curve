<template>
  <div>
    <svg />
  </div>
</template>

<script>
import * as d3 from "d3";
import _ from "lodash";

export default {
  props: ["series"],
  data() {
    return {
      chart: null
    };
  },
  watch: {
    series(val) {
      if (this.chart != null) this.chart.remove();
      this.renderChart(val);
    }
  },
  methods: {
    renderChart(series_val) {
      const margin = 100;
      const svg_width = 1000;
      const svg_height = 600;
      const chart_width = svg_width - margin;
      const chart_height = svg_height - 2 * margin;
      const barWidth = (chart_width / series_val.length) - 0.2

      const svg = d3
        .select("svg")
        .attr("width", svg_width)
        .attr("height", svg_height)

      this.chart = svg
        .append("g")
        .attr("transform", `translate(${margin}, ${margin})`)

      const maxY = _.maxBy(series_val, "total_infected").total_infected

      const yScale = d3
        .scaleLinear()
        .range([chart_height, 0])
        .domain([0, maxY])

      this.chart.append("g").call(d3.axisLeft(yScale).ticks(5));

      const xScale = d3
        .scaleTime()
        .range([0, chart_width])
        .domain([series_val[0].day, series_val[series_val.length -1].day])

      const xAxis = d3
        .axisBottom(xScale)
        .tickFormat(d3.timeFormat("%B"))
        // .tickFormat(d3.timeFormat("%m/%d"))
        .ticks(12)

      this.chart
        .append("g")
        .attr("transform", `translate(0, ${chart_height})`)
        .call(xAxis)
        .selectAll("text")
        .style("text-anchor", "start")

      const barGroups = this.chart
        .selectAll("rect")
        .data(series_val)
        .enter()

      barGroups
        .append("rect")
        .attr("class", "bar")
        .attr("x", o => xScale(o.day))
        .attr("y", o => yScale(o.total_infected))
        .attr("height", o => chart_height - yScale(o.total_infected))
        .attr("width", barWidth)
        .on("mouseenter", function(actual, i) {
          d3.select(this)
            .transition()
            .duration(100)
            .attr("opacity", 0.6)
          barGroups
            .append("text")
            .attr("class", "value")
            .attr("x", o => xScale(o.day) + barWidth / 2)
            .attr("y", o => yScale(o.total_infected) - 20)
            .attr("text-anchor", "middle")
            .text((o, idx) => {
              return idx !== i ? "" : `${o.day.formatMMDD()} - ${o.total_infected} total_infected`;
            })
        })
        .on("mouseleave", function() {
          d3.selectAll(".total_infected").attr("opacity", 1)

          d3.select(this)
            .transition()
            .duration(300)
            .attr("opacity", 1)
            .attr("x", o => xScale(o.day))
            .attr("width", barWidth)

          svg.selectAll(".value").remove()
        })

      svg
        .append("text")
        .attr("class", "label")
        .attr("x", -(chart_height / 2) - margin)
        .attr("y", 12)
        .attr("transform", "rotate(-90)")
        .attr("text-anchor", "middle")
        .text("total_infected")

      svg
        .append("text")
        .attr("class", "title")
        .attr("x", chart_width / 2 + margin)
        .attr("y", 40)
        .attr("text-anchor", "middle")
        .text("All who are and were total_infected")
    }
  }
};
</script>