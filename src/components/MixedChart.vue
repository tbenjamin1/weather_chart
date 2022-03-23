<template>
  <div>
    <div id="chart">
        <apexchart type="line" height="350" :options="chartOptions" :series="series"></apexchart>
      </div>
  </div>
</template>

<script>
import Vue from "vue";
import VueApexCharts from "vue-apexcharts";
Vue.use(VueApexCharts);
Vue.component("apexchart", VueApexCharts);
export default {
  props: {
    temperature: {
      type: Array,
    },
    timeStamp: {
      type: Array,
    },
    windSpeed:{
      type:Array,
    }
  },
 data: function () {
    return {
          series: [{
            name: 'wind speed Km/H',
            type: 'column',
            data: this.windSpeed,
          }, {
            name: ' temperature  in °C',
            type: 'area',
            data: this.temperature,
          },
          ],
          chartOptions: {
            chart: {
              height: 350,
              type: 'line',
              stacked: false,
            },
            stroke: {
              width: [0, 2, 5],
              curve: 'smooth'
            },
            plotOptions: {
              bar: {
                columnWidth: '50%'
              }
            },
            
            fill: {
              opacity: [0.85, 0.25, 1],
              gradient: {
                inverseColors: false,
                shade: 'light',
                type: "vertical",
                opacityFrom: 0.85,
                opacityTo: 0.55,
                stops: [0, 100, 100, 100]
              }
            },
            labels: this.timeStamp,
            markers: {
              size: 0
            },
            xaxis: {
              type: 'datetime',
              labels: {
            format: "ddd H:mm ",
          },
            },
            yaxis: {
              title: {
                text: 'Measures',
              },
              min: 0
            },
            tooltip: {
              shared: true,
              intersect: false,
              y: {
                formatter: function (y) {
                  if (typeof y !== "undefined") {
                    return y.toFixed(0) + " ";
                  }
                  return y;
            
                }
              }
            }
          },
          
    }
        },
};
</script>

<style scoped>
</style>