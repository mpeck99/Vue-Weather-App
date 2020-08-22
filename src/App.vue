<template>
  <div id="app">
    <main>
      <div class="search">
        <label for="name">Location</label>
        <input type="text" name="search" id="search" placeholder="Search" v-model="query" @keyup.enter="fetchForecast">
        <button @click="fetchForecast" class="searchBtn">Search</button>
      </div>
      <div class="weather-cards" v-for="weather in weatherData" :key="weather.list">
        <div class="card">
          <p>{{weather.dt_txt}}</p>
          <img v-bind:src="'https://openweathermap.org/img/w/' + weather.weather[0].icon +'.png'">
          <h2>{{weather.weather[0].main}}</h2>
          <p>{{weather.weather[0].description}}</p>
          <ul>
            <li>{{weather.main.temp_min}}</li>
            <li>{{weather.main.temp_max}}</li>
          </ul>
        </div>
      </div>
    </main>
  </div>
</template>

<script>   
export default {
  name: 'App',
  data (){
    return {
      api_key : '4de4a8d80078d333a4a72f1c11d87820',
      forecast_url: 'https://api.openweathermap.org/data/2.5/',
      weatherData : [],
      query: '',
    }
  },
  methods: {
    fetchForecast(){
     fetch(`${this.forecast_url}forecast?q=${this.query},us&appid=${this.api_key}&units=imperial`)
      .then(res => {
        return res.json();
      }).then(this.storeData);
    },
    storeData(data) {
      //clearing the data array when a new search is done
      this.weatherData = [];

      //looping through the data.list to only return the 5 day forecast and not the data for every 3 hours
      for(var i = 0; i < data.list.length; i+=8){
        this.weatherData.push(data.list[i]);
      }
      
      //looping through the weatherDate array to fix temps and dates
      for(var x = 0; x < this.weatherData.length; x++){
        var dateMin = parseInt(this.weatherData[x].main.temp_min);
        var dateMax = parseInt(this.weatherData[x].main.temp_max);
        var date = new Date(this.weatherData[x].dt_txt.split(" ")[0]).toDateString();

        this.weatherData[x].main.temp_min = dateMin;
        this.weatherData[x].main.temp_max = dateMax;
        this.weatherData[x].dt_txt = date;
      }
    }
  },
}
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
</style>
