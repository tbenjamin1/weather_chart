<template>
  <div id="app">
    <h1>7 Days Forecast</h1>
    <span class="indicator">
      <strong>X axis : <small>Local timeStamp</small> </strong>
      <strong
        >Y axis :
        <small
          >Temperature Converted in degree Celsius (symbol: °C)</small
          
        ></strong
      >
    </span>

    <span>
      <span v-if="isWeather" class="now-result">
        <span class="cloud">
          <img :src="getImg(currentWeather.weather.icon)" alt="cloud" />
        </span>
        <span class="temperature">
          <strong>{{ currentWeather.temp }} °C</strong>
        </span>

        <span class="Humidity-wind">
          <strong>Time Zone: <small> {{ckeckWeatherData.timezone}} </small></strong>
          <strong>
            precipitation : <small>{{ currentWeather.precip }}% </small>
          </strong>
          <strong>
            Humidity: <small>{{ currentWeather.rh }}% </small>
          </strong>
          <strong
            >Wind: <small> {{ currentWeather.wind_spd }} km/h</small></strong
          >
          <strong
            >Time:
            <small>
              {{ fortmatTime(currentWeather.timestamp_local) }}
            </small></strong
          >
          <strong>
            description:
            <small>{{ currentWeather.weather.description }} </small>
          </strong>
        </span>
      </span>
      <span v-else > no weather measurements </span>
    </span>

    <div class="chart-container">
      <div>
        <ApexChart
          v-if="allWeather"
          :temperature="temperature"
          :timeStamp="timeStamp"
        />
      </div>
      <ColumnDatalabels
        v-if="allWeather"
        :temperature="temperature"
        :timeStamp="timeStamp"
      />
    </div>
    <div>
      <MixedChart
        v-if="allWeather"
        :temperature="temperature"
        :timeStamp="timeStamp"
        :windSpeed="windSpeed"
      />
    </div>
  </div>
</template>

<script>
import ApexChart from "./components/chart/ApexChart.vue";
import ColumnDatalabels from "./components/ColumnDatalabels.vue";
import MixedChart from "./components/MixedChart.vue";
import axios from "axios";
import moment from "moment";

export default {
  name: "App",
  components: {
    ApexChart,
    ColumnDatalabels,
    MixedChart,
  },
  data: function () {
    return {
      allWeather: null,
      temperature: null,
      timeStamp: null,
      windSpeed: null,
      temperatureIcon: null,
      isWeather: false,
      weatherTime: null,
      currentWeather: null,
      ckeckWeatherData: "",
      location: null,
      gettingLocation: false,
      errorStr: null,
      longitude: null,
      latitude: null,
    };
  },
  methods: {
    fortmatTime(time) {
      console.log(time);

      return moment(time).format("DD MMMM YYYY HH:mm ");
    },
    /**
     * method to current set weather icon dynamically
     */
    getImg(icon) {
      return `https://www.weatherbit.io/static/img/icons/${icon}.png`;
    },

    getLocation() {
      if (!("geolocation" in navigator)) {
        this.errorStr = "Geolocation is not available.";
        return;
      }

      this.gettingLocation = true;
      // get position
      navigator.geolocation.getCurrentPosition(
        (pos) => {
          this.gettingLocation = false;
          this.location = pos;

          this.latitude = pos.coords.latitude;
          this.longitude = pos.coords.longitude;
        },
        (err) => {
          this.gettingLocation = false;
          this.errorStr = err.message;
        }
      );
    },

    async fetchWeatherData() {
     
      console.log("latitude and longitude");

      console.log("latitude and longitude", this.latitude, this.longitude);

      var options = {
        method: "GET",
        url: "https://weatherbit-v1-mashape.p.rapidapi.com/forecast/3hourly",
        params: { lat: "-1.9584187", lon: "30.0696192" },
        headers: {
          "X-RapidAPI-Host": "weatherbit-v1-mashape.p.rapidapi.com",
          "X-RapidAPI-Key":
            "ddfc51f560msh5c3a0ff4279ef02p1bc82ajsn5bafac68ab9a",
        },
      };

      try {
        const response = await axios.request(options);
        const weatherData = response.data;
        let storageCheck = localStorage.getItem("weatherData");
        if (storageCheck) {
          localStorage.clear();
          localStorage.setItem("weatherData", JSON.stringify(weatherData));
        }
        this.allWeather = JSON.parse(localStorage.getItem("weatherData"));
        this.allWeather = this.allWeather.data;
      } catch (error) {
        this.allWeather = JSON.parse(localStorage.getItem("weatherData"));
        this.allWeather = this.allWeather.data;
      }

      const ckeckWeatherData = JSON.parse(localStorage.getItem("weatherData"));
      console.log("ckeck", ckeckWeatherData);
      this.ckeckWeatherData = ckeckWeatherData;
      this.checkWeather();

      /**
       * looping through weather data to get timestamp_local
       */
      let timestamp_local = this.allWeather.map((time) => {
        return time.timestamp_local;
      });
      this.timeStamp = timestamp_local;

      let windSpeed = this.allWeather.map((wind) => {
        return wind.wind_spd;
      });

      this.windSpeed = windSpeed;

      /**
       * looping through weather data to get temperature for each timeStamp
       */
      let temperature = this.allWeather.map((temp) => {
        return temp.temp;
      });
      this.temperature = temperature;
    },

    checkWeather() {
      this.ckeckWeatherData.data.map((weather) => {
        this.weatherTime = weather.timestamp_local;

        let apidate = moment(this.weatherTime).format("DD MM YYYY HH");
        let nowDate = moment().format("DD MM YYYY HH");
    

        if (apidate <= nowDate) {
          console.log("api date");

          this.currentWeather = weather;
          this.isWeather = true;
        }
        
        // console.log("api date", apidate);
        // console.log("now", nowDate);

      });
    },
  },
  created() {
    this.getLocation();
    this.fetchWeatherData();
  },

  mounted: function () {
    this.timer = setInterval(() => {
      this.checkWeather();
    }, 3600000);
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
h1 {
  margin-bottom: 4rem;
  text-decoration: underline;
}
.indicator {
  display: flex;
  flex-direction: column;
  margin: 4rem;
  text-align: start;
}
small {
  color: gray;
}
.temperature {
  text-align: center;
  font-size: 40px;
  padding: 10px 10px 0px 0px;
}
.now-result {
  display: flex;
  margin-bottom: 3rem;
}
.cloud {
  margin-right: 2rem;
}
.Humidity-wind {
  display: flex;
  flex-direction: column;
  text-align: start;
  padding: 10px 10px 0px 0px;
}
.chart-container {
  width: 80%;
}
</style>
