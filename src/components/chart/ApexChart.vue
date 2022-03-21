<template>
  <div>
    <div id="chart">
      <apexchart
        type="area"
        height="350"
        :options="chartOptions"
        :series="series"
      ></apexchart>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import VueApexCharts from "vue-apexcharts";
Vue.use(VueApexCharts);
Vue.component("apexchart", VueApexCharts);
export default {
    props:{
    temperature:{
      type:Array
    },
      timeStamp:{
        type:Array
      },
    },
 
  data: function () {
    return {
      series: [
        {
          name: "Temperature",
          data: this.temperature,
        },
      ],
      chartOptions: {
        chart: {
          height: 350,
          type: "area",
        },
        dataLabels: {
          enabled: true,    
              
          formatter: function (val) {

            return val + "°C" ;
          },
          offsetY: -20,
          style: {
            fontSize: "12px",
            colors: ["#304758"],
          },
        },
        stroke: {
          curve: "smooth",
        },

        xaxis: {
          type: "datetime",
          labels: {
            format: "ddd HH:mm ",
          },

          categories: this.timeStamp,                
        },
          annotations: {
          position: "front",
          xaxis: [
            {
             x: new Date().getTime(),
              // x2: new Date().getTime(),
              borderColor: "#111",
              label: {
                borderColor: "transparent",
                style: {
                  fontSize: "14px",
                  color: "#666",
                  background: "#e7e7e7",
                  padding: {
                    left: 15,
                    right: 15,
                    top: 10,
                    bottom: 12,
                  },
                },
                text: "now",
              },
            },
          ],
        },
        tooltip: {
          x: {
            format: "dd  MMM y HH:mm ",
          },
        },
        yaxis: {
          title: {
            text: "levels",
          },
          min: 0,
        },
        // title: {
        //       text: 'Monthly Inflation in Argentina, 2002',
        //       floating: true,
        //       offsetY: 330,
        //       align: 'center',
        //       style: {
        //         color: '#444'
        //       }
        //     }
        
  // legend: {
  //   show: false
  // }
        
      },
    };
  },
};
</script>

<style scoped>

</style>