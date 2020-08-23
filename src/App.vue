<template>
  <div id="app">
    <h1>5 day forecast</h1>
    <section aria-label="Search for your a five day forecast">
      <div class="form-group">
        <label for="search" class="sr-only">Location</label>
        <input type="text" name="search" id="search" placeholder="Search" v-model="query" @keyup.enter="fetchForecast">
        <button @click="fetchForecast" class="searchBtn">Search</button>
      </div>
      <h2>{{location}}</h2>
      <div class="card-wrapper" >
        <div class="card" v-for="weather in weatherData" :key="weather.list">
          <time>{{weather.dt_txt}}</time>
          <div class="status">
            <p class="temp">{{weather.main.temp}}</p>
            <img v-bind:src="require(`${weather.weather[0].icon}`)" class="status-icn" v-bind:alt="`${weather.dt_txt} ${weather.weather[0].description}`">
            <p>{{weather.weather[0].description}}</p>
          </div>
          <p class="temp">Feels like {{weather.main.feels_like}}</p>
        </div>
      </div>
    </section> 
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
      location: '',
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
      this.location = data.city.name;
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

        //replacing the icon from the api with an svg
        if(this.weatherData[x].weather[0].icon == "01d" || this.weatherData[x].weather[0].icon == "01n"){
          this.weatherData[x].weather[0].icon = './assets/icn-sunny.svg';
        }
        
        else if(this.weatherData[x].weather[0].icon == "02d" || this.weatherData[x].weather[0].icon == "02n"){
          this.weatherData[x].weather[0].icon = './assets/icn-partly-cloudy.svg';
        }

        else if(this.weatherData[x].weather[0].icon == "03d" || this.weatherData[x].weather[0].icon == "03n" || this.weatherData[x].weather[0].icon == "04d" || this.weatherData[x].weather[0].icon == "04n") {
          this.weatherData[x].weather[0].icon = './assets/icn-cloudy.svg';
        }

        else if(this.weatherData[x].weather[0].icon == "09d" || this.weatherData[x].weather[0].icon == "09n"){
          this.weatherData[x].weather[0].icon = './assets/icn-light-rain.svg';
        }

        else if(this.weatherData[x].weather[0].icon == "10d" || this.weatherData[x].weather[0].icon == "10n"){
          this.weatherData[x].weather[0].icon = './assets/icn-hvy-rain.svg';
        }
        else if(this.weatherData[x].weather[0].icon == "11d" || this.weatherData[x].weather[0].icon == "11n"){
          this.weatherData[x].weather[0].icon = './assets/icn-thunderstorm.svg';
        }
        
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
    font-family: 'Lato', sans-serif;
    text-align: center;
    text-transform: capitalize;
    
  background-color: #FF6B6B;
   
  }
  #app {
    display: flex;
    flex-direction: column;
  }
  
  h1 {
    margin-bottom: 0;

    font-family: 'Roboto', sans-serif;
  }

  p {
    margin: 0;
  }

  section {
    flex-grow: 1;

    display: grid;
    grid-template-columns: 100%;
    grid-template-rows: 6rem 1fr;

    padding: 1rem;

    
  }
  .form-group {
    grid-row: 1 / 2;
    justify-self: center;
    align-self: center;
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
    width: 12rem;
    height: 18rem;

    display: grid;
    grid-template-columns: 100%;
    grid-template-rows: auto auto auto auto;
    align-items: center;
    margin: 1rem;

    text-align: center;

    border-radius: 0.25rem;
    
     background-color: #F7FFF7;
  }

  .status .temp {
    font-size: 2.5rem;
  }

  .temp {
    position: relative;
  }

  .temp::after {
    content: '\00b0';

    position: absolute;
    top: .09rem;

    margin-left: 0.1rem;
  }

  .sr-only {
    position: absolute ;
    left: -10000px;
    width: 1px;
    height: 1px;
    top: auto;
    overflow: hidden;
  }

</style>
