<template>
  <div id="app">
    <main>
      <div class="search">
        <label for="name">Location</label>
        <button @click="fetchForecast" class="searchBtn">Search</button>
      </div>
      <!-- {{weather.city.name}} -->
      <div class="weather-cards" v-for="weather in weatherData" :key="weather.list">
        <div class="card">
          <h2>{{weather.main.temp}}</h2>
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
    fetchForecast(e){
      if(e.key === "Enter"){
        fetch(`${this.forecast_url}forecast?q=${this.query},us&appid=${this.api_key}&units=imperial`)
          .then(res => {
            return res.json();
          }).then(this.storeData);
      }
    },

    storeData(data) {
      for(var i = 0; i < data.list.length; i+=8){
        this.weatherData.push(data.list[i]);
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
