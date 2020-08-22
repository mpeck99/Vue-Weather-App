<template>
  <div id="app">

      <div class="form-group">
        <label for="name">Location</label>
        <input type="text" name="search" id="search" placeholder="Search" v-model="query" @keyup.enter="fetchForecast">
        <button @click="fetchForecast" class="searchBtn">Search</button>
      </div>
      <div class="card-wrapper" >
        <div class="card" v-for="weather in weatherData" :key="weather.list">
          <time>{{weather.dt_txt}}</time>
          <p class="temp">{{weather.main.temp}}</p>
          <p>{{weather.weather[0].main}}</p>
          <p class="temp">Feels like {{weather.main.feels_like}}</p>
        </div>
      </div>

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
        const temp = parseInt(this.weatherData[x].main.temp),
              tempMin = parseInt(this.weatherData[x].main.temp_min),
              tempMax = parseInt(this.weatherData[x].main.temp_max),
              feelsLike = parseInt(this.weatherData[x].main.feels_like),
              date = new Date(this.weatherData[x].dt_txt.split(" ")[0]).toDateString().split(' 2020')[0];

        this.weatherData[x].main.temp = temp;
        this.weatherData[x].main.temp_min = tempMin;
        this.weatherData[x].main.temp_max = tempMax;
        this.weatherData[x].main.feels_like = feelsLike;
        this.weatherData[x].dt_txt = date;
      }
    }
  },
}
</script>

<style>
    * {
        box-sizing: border-box;
    }
  html, body {
    height: 100%;

    padding: 0;
    margin: 0;

    font-size: 16px;
  }

  #app {
    min-height: 100vh;

    display: grid;
    grid-template-columns: 100%;
    grid-template-rows: 6rem 1fr;

    padding: 1rem;

    font-family: 'Dosis', sans-serif;
  }

  .form-group {
    /* grid-column: 1/2; */
    grid-row: 1 / 2;
    justify-self: center;
    align-self: flex-end;
  }

  .card-wrapper {
    width: 90%;

    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    justify-self: center;
    align-self: center;
    grid-column: 1;
    grid-row: 2 / 3;
  }

  .card {
    width: 10rem;
    height: 18rem;

    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
  
    margin: 1rem;
  }

  .temp {
    position: relative;
  }

  .temp::after {
    /* content:; */

    position: absolute;
  }

</style>
