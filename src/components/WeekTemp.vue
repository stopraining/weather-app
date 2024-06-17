<template>
  <div class="weather">
    <div class="container-fluid week" v-if="showChart">
      <Line v-if="loaded" :data="data" :options="options" />
    </div>
  </div>
</template>

<script>
import axios from "axios";

import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend,
} from "chart.js";
import { Line } from "vue-chartjs";
import ChartDataLabels from "chartjs-plugin-datalabels";

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend,
  ChartDataLabels
);
export let weatherDescribe = []; //for tooltips

export default {
  name: "WeekTemp",
  props: ["areaI", "showChart", "counrtyI"],
  data() {
    return {
      apiKey: "CWB-CD62E333-DFC2-48D6-9D5F-B6CEE673C2EF",
      locations: [
        { name: "基隆市", code: "F-D0047-051" },
        { name: "新北市", code: "F-D0047-071" },
        { name: "臺北市", code: "F-D0047-063" },
        { name: "桃園市", code: "F-D0047-007" },
        { name: "新竹縣", code: "F-D0047-011" },
        { name: "新竹市", code: "F-D0047-055" },
        { name: "苗栗縣", code: "F-D0047-015" },
        { name: "臺中市", code: "F-D0047-075" },
        { name: "彰化縣", code: "F-D0047-019" },
        { name: "南投縣", code: "F-D0047-023" },
        { name: "雲林縣", code: "F-D0047-027" },
        { name: "嘉義縣", code: "F-D0047-031" },
        { name: "嘉義市", code: "F-D0047-059" },
        { name: "臺南市", code: "F-D0047-079" },
        { name: "高雄市", code: "F-D0047-067" },
        { name: "屏東縣", code: "F-D0047-035" },
        { name: "臺東縣", code: "F-D0047-039" },
        { name: "花蓮縣", code: "F-D0047-043" },
        { name: "宜蘭縣", code: "F-D0047-003" },
        { name: "澎湖縣", code: "F-D0047-047" },
        { name: "連江縣", code: "F-D0047-083" },
        { name: "金門縣", code: "F-D0047-087" },
      ],
      weatherDescribe: [], //天氣概述
      loaded: false, //for load chart
      //for chart
      data: {
        labels: [],
        datasets: [
          {
            label: "最低溫度",
            backgroundColor: "rgb(100,200,200)",
            borderColor: "rgb(100,200,200)",
            data: [],
            pointStyle: "rectRounded",
            pointHoverBackgroundColor: "skyblue",
            tension: 0.1,
          },
          {
            label: "最高溫度",
            backgroundColor: "rgb(250,128,144)",
            borderColor: "rgb(250,128,144)",
            data: [],
            pointStyle: "rectRounded",
            pointHoverBackgroundColor: "orange",
            tension: 0.1,
          },
        ],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          datalabels: {
            align: "top",
            color: "white",
            font: {
              size: 10,
            },
          },
          legend: {
            display: true,
            position: "top",
            useBorderRadius: true,
            labels: {
              color: "white",
            },
          },
          tooltip: {
            callbacks: {
              label: function (tooltipItems) {
                return (
                  tooltipItems.dataset.label + ":" + tooltipItems.raw + "°C"
                );
              },
              afterLabel: function (tooltipItems) {
                // console.log(tooltipItems)
                return weatherDescribe[tooltipItems.dataIndex];
              },
            },
          },
        },
        scales: {
          y: {
            ticks: {
              color: "white",
              stepSize: 5,
              font: {
                weight: "bold",
              },
            },
            grid: {
              display: false,
            },
            max: 45,
          },
          x: {
            ticks: {
              color: "white",
              font: {
                weight: "bold",
              },
            },
            grid: {
              display: false,
            },
          },
        },
      },
    };
  },
  components: {
    Line,
  },
  watch: {
    async showChart(newValue) {
      if (newValue) {
        this.loaded = false;
        let weekCountryCode = this.locations[this.counrtyI].code;
        let getWeek = await axios.get(
          `https://opendata.cwa.gov.tw/api/v1/rest/datastore/${weekCountryCode}?Authorization=${this.apiKey}`
        );
        let weekArea = getWeek.data.records.locations[0].location[this.areaI];
        // console.log(JSON.stringify(weekArea.weatherElement[12].time))

        let LowTempData = weekArea.weatherElement[8].time;
        let highTempData = weekArea.weatherElement[12].time;
        let weatherDes = weekArea.weatherElement[6].time;

        this.data.datasets[0].data = this.weekArray(LowTempData);
        this.data.datasets[1].data = this.weekArray(highTempData);
        this.data.labels = this.weekDateArray(LowTempData); //date x asix
        weatherDescribe = this.weekArray(weatherDes); //tooltip show
        // console.log(weatherDescribe)
        this.loaded = true;
      }
    },
  },
  methods: {
    weekArray(dataArray) {
      //low and high temp array data clean
      let resultArray = [];
      dataArray.map((x) => {
        resultArray.push(x.elementValue[0].value);
      });

      return resultArray;
    },
    weekDateArray(dataArray) {
      //x-axis clean
      let resultArray = [];
      dataArray.map((x) => {
        let dateString = x.startTime;
        dateString = dateString.replace(/2023-/g, "");
        dateString = dateString.replace(/-/g, "/");
        dateString = dateString.replace(/06:00:00/g, "上午6時");
        dateString = dateString.replace(/18:00:00/g, "下午6時");
        dateString = dateString.replace(/12:00:00/g, "中午12時");
        resultArray.push(dateString.split(" "));
      });
      return resultArray;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* <!-- <style lang="scss">
    @import '~bootstrap/scss/bootstrap'; --> */

h1 {
  margin: 30px 0px;
  color: white;
  opacity: 0.7;
}

h2 {
  margin: 0px;
  font-size: 25pt;
  color: #002d6d;
}

ul {
  list-style-type: none;
  padding: 0;
}
li {
  /*display: inline-block;*/
  margin: 0 10px;
}
a {
  color: darkgreen;
}

.week {
  margin-top: 30px;
  margin-bottom: 50px;
  padding-top: 10px;
  width: 90%;
  height: 350px;
}
</style>
