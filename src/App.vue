<template>
  <div id="app">
    <h1>7 Days Forecast</h1>
    <ApexChart :temperature="temperature" :timeStamp="timeStamp" />
  </div>
</template>

<script>
import ApexChart from "./components/chart/ApexChart.vue";
import axios from "axios";
export default {
  name: "App",
  components: {
    ApexChart,
  },

  data: function () {
    return {
      allWeather: null,
      temperature:null,
      timeStamp:null,
      temperatureIcon:null,
      
    };
  },

  methods: {
    async fetchWeatherData() {

      /**
       * getting Weather data stored in local storage  
       */
      const retrievedWeatherData = localStorage.getItem("weatherData");
      let data = JSON.parse(retrievedWeatherData);
      this.allWeather = data.data;

      /**
       * looping through weather data to get timeStamp_local
       */
      let timeStamp_local =  data.data.map(time =>{
        return time.timestamp_local;
      
      })
      this.timeStamp =timeStamp_local;
  
      /**
       * looping through weather data to get temperature for each timeStamp
       */
      let temperature = data.data.map(temp =>{
        return temp.temp;
      
      })
     
        this.temperature =temperature;
        console.log( this.temperature)

      //     var options = {
      //   method: 'GET',
      //   url: 'https://weatherbit-v1-mashape.p.rapidapi.com/forecast/3hourly',
      //   params: {lat: '35.5', lon: '-78.5'},
      //   headers: {
      //     'x-rapidapi-host': 'weatherbit-v1-mashape.p.rapidapi.com',
      //     'x-rapidapi-key': 'cafd798d1fmsh0249b12726a227dp16a61djsn426b2d13c811'
      //   }
      // };

      // axios.request(options).then(function (response) {

      //   const weatherData=response.data;
      // localStorage.setItem('weatherData', JSON.stringify(weatherData));
      // const retrievedWeatherData = localStorage.getItem('weatherData');

      //    console.log('all response',retrievedWeatherData);
      //   this.allWeather=retrievedWeatherData;

      // }).catch(function (error) {
      //   console.error(error);

      // });


    },
  },
  created() {
    this.fetchWeatherData();
  },
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
h1{
  margin-bottom: 4rem;
}
</style>
