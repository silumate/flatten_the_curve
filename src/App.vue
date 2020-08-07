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
const lodashClonedeep = require('lodash.clonedeep');

Date.prototype.formatMMDD = function(){
    return (this.getMonth() + 1) + 
    "/" +  this.getDate()
}

const R_0 = 2.0
const latent_period = 3
const infectious_period = 10
let tmp_infectous = new Array(latent_period + infectious_period).fill(0)  //each value in array is number of people for that day/index that are infectuous
tmp_infectous[0] = 1
const day_0 = {
  day: new Date("2020-01-01T00:00:00"),
  total_infected: 1,
  total_infectuous: 0,
  total_uninfected: 100000000,
  infectous_at_stage: tmp_infectous
}

function next_day_stats(today) {
  let tomorrow = lodashClonedeep(today)
  tomorrow.day.setDate(tomorrow.day.getDate() + 1)
  // shift all infected peple into their next stage
  for (let i = latent_period + infectious_period - 1; i >= 1; i--) {
    tomorrow.infectous_at_stage[i] = tomorrow.infectous_at_stage[i-1]
  }
  // calcualte how many get infected today and
  // how many people are capable of infecting others (they are in stage/day 3 to 10 or their run)
  let new_infections = 0.0
  let total_infectuous = 0
  for (let i = latent_period; i < latent_period + infectious_period; i++) {
    let cohort = tomorrow.infectous_at_stage[i] * R_0 / infectious_period
    new_infections += cohort
    total_infectuous += tomorrow.infectous_at_stage[i]
  }
  //apply some rounding
  if (new_infections < 1) {
    new_infections = 1
  } else if (new_infections < 2) {
    new_infections = 2
  } else {
    new_infections = Math.floor(new_infections)
  }
  tomorrow.infectous_at_stage[0] = new_infections
  tomorrow.total_infected += new_infections
  tomorrow.total_infectuous = total_infectuous

  return tomorrow
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
      const endDate = new Date("2020-08-01T00:00:00")
      let stats = []
      let x = 0
      let stat = day_0

      for (;;) {
        stat = next_day_stats(stat)
        if (stat.day > endDate) break
        stats.push(stat)
        x++
        if (x > 367) break
      }
      this.series = stats
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
