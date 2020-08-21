<template>
  <div id="app">
    <main>
      <div class="search">
        <label for="name">Location</label>
        <input type="text" name="search" id="search" placeholder="Search" v-model="query" @keypress="fetchForecast">
      </div>
      <div class="weather-cards" v-if="typeof weather.main !='undefied'">
        {{weather.name}}
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
      weather : {},
      query: '',
    }
  },
  methods: {
    fetchForecast(e){
      if(e.key === "Enter"){
        fetch(`${this.forecast_url}forecast?q=${this.query},us&appid=${this.api_key}`)
          .then(res => {
            return res.json();
            
          }).then(this.storeData);
      }
    },

    storeData(data) {
      console.log(data.list[1].weather[0].main);
      this.weather = data;
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
