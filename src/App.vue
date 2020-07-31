<template>
  <div id="app">
    <!---
    <BarChart />
    <LineChart />
    <HelloWorld msg="Welcome to Your Vue.js App"/>
    --->
    <form action="#">
      <div class="form-group">
<!--
        <input
          type="text"
          placeholder="owner/repo Name"
          v-model="repository"
          class="col-md-2 col-md-offset-5"
        />
-->
        <button v-on:click='runSimulation'>Run Simulation</button>
      </div>
    </form>
    <BarTimeSeries :series="series" />
  </div>
</template>

<script>
/*
import LineChart from "./components/LineChart.vue";
import BarChart from "./components/BarChart.vue";
import HelloWorld from "./components/HelloWorld.vue";
*/
import BarTimeSeries from "./components/BarTimeSeries.vue"

Date.prototype.formatMMDD = function(){
    return (this.getMonth() + 1) + 
    "/" +  this.getDate()
}

export default {
  name: "App",
  components: {
    BarTimeSeries
    /*
    HelloWorld,
    BarChart,
    LineChart
    */
  },
  data() {
    return {
      series: []
    }
  },
  methods: {
    runSimulation() {
      const startDate = new Date("2020-01-01T00:00:00")
      const endDate = new Date("2021-01-01T00:00:00")

      let peep_infected = []
      let x = 0
      for (;;) {
        const newDay = new Date(startDate.valueOf())
        newDay.setDate(newDay.getDate() + x)
        if (newDay >= endDate) break
        const sick = 340000 - ((x - 183)**2)
        peep_infected.push( {day: newDay, infected: sick} )
        x++
        if (x > 367) break
      }
      this.series = peep_infected
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.bar {
  fill: #319bbe;
}
</style>
