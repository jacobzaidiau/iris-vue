<template>
  <div id="app" class="container">
    <div class="jumbotron jumbotron-fluid">
      <h3 class="display-4 text-center">Kia ora</h3>
    </div>
    <div class="row">
      <div class="col">
        <p>
          This page was created with ASP.NET Core, Vue.js, Bootstrap and ML.NET! I've taken the traditional
          Iris Data and applied a K-Means clustering algorithm to it. I've used Charts.js to
          display the results in a scattering chart, and added a button to toggle between the petal
          and sepal widths and lengths. The back-end is running on a Docker image deployed onto Heroku
          which you can access HERE. Cheers.
        </p>
      </div>
    </div>
    <div class="row mt-5">
      <div class="col">
        <h4 class="text-center">{{isPetal?"Petal":"Sepal"}} Data</h4>
      </div>
    </div>
    <div class="row mt-2" v-if="datacollection !== null && chartOptions !== null">
      <div class="col">
        <scatter-chart :chart-data="datacollection" :options="chartOptions"></scatter-chart>
      </div>
    </div>
    <div class="row mt-3 text-center">
      <div class="col">
        <button
          type="button"
          class="btn btn-primary"
          v-on:click="switchComponent()"
        >See {{isPetal ? "Sepal":"Petal"}} Data</button>
      </div>
    </div>
    <div class="jumbotron jumbotron-fluid">
      <h3 class="display-4 text-center">Ng&amacr; mihi!</h3>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import ScatterChart from "./components/ScatterChart.js";
export default {
  name: "App",
  components: {
    ScatterChart
  },
  methods: {
    switchComponent: function() {
      if (!this.isPetal) {
        this.datacollection = JSON.parse(this.petalData);
        this.isPetal = !this.isPetal;
      } else {
        this.datacollection = JSON.parse(this.sepalData);
        this.isPetal = !this.isPetal;
      }
    }
  },
  data() {
    return {
      isPetal: false,
      datacollection: {},

      sepalData: "",
      petalData: "",

      sepalOptions: "",
      petalOptions: "",

      chartOptions: null,

      sepalChartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          xAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: "Width"
              }
            }
          ],
          yAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: "Length"
              }
            }
          ]
        }
      }
    };
  },
  async created() {
    var SepalClusterOne = [];
    var SepalClusterTwo = [];
    var SepalClusterThree = [];
    var PetalClusterOne = [];
    var PetalClusterTwo = [];
    var PetalClusterThree = [];

    const { data } = await axios.get(
      "https://zaidi004.herokuapp.com/api/cluster"
    );

    data.forEach(d => {
      const { sepalWidth, sepalLength, petalWidth, petalLength, clusterId } = d;

      if (clusterId == 1) {
        SepalClusterOne.push({ x: sepalWidth, y: sepalLength });
        PetalClusterOne.push({ x: petalWidth, y: petalLength });
      } else if (clusterId == 2) {
        SepalClusterTwo.push({ x: sepalWidth, y: sepalLength });
        PetalClusterTwo.push({ x: petalWidth, y: petalLength });
      } else {
        SepalClusterThree.push({ x: sepalWidth, y: sepalLength });
        PetalClusterThree.push({ x: petalWidth, y: petalLength });
      }
    });
    var sepalDataSet = {
      datasets: [
        {
          label: "Cluster One",
          fill: false,
          borderColor: "#f87979",
          backgroundColor: "#f87979",
          data: SepalClusterOne
        },
        {
          label: "Cluster Two",
          fill: false,
          borderColor: "#337ab7",
          backgroundColor: "#337ab7",
          data: SepalClusterTwo
        },
        {
          label: "Cluster Three",
          fill: false,
          borderColor: "#5cb85c",
          backgroundColor: "#5cb85c",
          data: SepalClusterThree
        }
      ]
    };
    var petalDataSet = {
      datasets: [
        {
          label: "Cluster One",
          fill: false,
          borderColor: "#f87979",
          backgroundColor: "#f87979",
          data: PetalClusterOne
        },
        {
          label: "Cluster Two",
          fill: false,
          borderColor: "#337ab7",
          backgroundColor: "#337ab7",
          data: PetalClusterTwo
        },
        {
          label: "Cluster Three",
          fill: false,
          borderColor: "#5cb85c",
          backgroundColor: "#5cb85c",
          data: PetalClusterThree
        }
      ]
    };

    this.sepalData = JSON.stringify(sepalDataSet);
    this.petalData = JSON.stringify(petalDataSet);

    this.sepalOptions = JSON.stringify(this.sepalChartOptions);
    this.petalOptions = JSON.stringify(this.petalChartOptions);

    this.datacollection = JSON.parse(this.sepalData);
    this.chartOptions = JSON.parse(this.sepalOptions);
  }
};
</script>

<style>
html,
body {
  background-color: #e9ecef !important;
}
</style>
