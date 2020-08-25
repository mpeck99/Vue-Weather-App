<template>
  <div id="app">
    <h1>5 day forecast</h1>
    <section aria-label="Search for your a five day forecast">
      <div class="form-group">
        <label for="search" class="sr-only">Location</label>
        <input type="text" name="search" id="search" placeholder="City" v-model="city_query" @keyup.enter="fetchForecast">
        <select v-model="state_query" @keyup.enter="fetchForecast" placeholder="State">
          <option value="" class="disabled selected">State</option>
          <option value="AL">Alabama</option>
          <option value="AK">Alaska</option>
          <option value="AZ">Arizona</option>
          <option value="AR">Arkansas</option>
          <option value="CA">California</option>
          <option value="CO">Colorado</option>
          <option value="CT">Connecticut</option>
          <option value="DE">Delaware</option>
          <option value="DC">District Of Columbia</option>
          <option value="FL">Florida</option>
          <option value="GA">Georgia</option>
          <option value="HI">Hawaii</option>
          <option value="ID">Idaho</option>
          <option value="IL">Illinois</option>
          <option value="IN">Indiana</option>
          <option value="IA">Iowa</option>
          <option value="KS">Kansas</option>
          <option value="KY">Kentucky</option>
          <option value="LA">Louisiana</option>
          <option value="ME">Maine</option>
          <option value="MD">Maryland</option>
          <option value="MA">Massachusetts</option>
          <option value="MI">Michigan</option>
          <option value="MN">Minnesota</option>
          <option value="MS">Mississippi</option>
          <option value="MO">Missouri</option>
          <option value="MT">Montana</option>
          <option value="NE">Nebraska</option>
          <option value="NV">Nevada</option>
          <option value="NH">New Hampshire</option>
          <option value="NJ">New Jersey</option>
          <option value="NM">New Mexico</option>
          <option value="NY">New York</option>
          <option value="NC">North Carolina</option>
          <option value="ND">North Dakota</option>
          <option value="OH">Ohio</option>
          <option value="OK">Oklahoma</option>
          <option value="OR">Oregon</option>
          <option value="PA">Pennsylvania</option>
          <option value="RI">Rhode Island</option>
          <option value="SC">South Carolina</option>
          <option value="SD">South Dakota</option>
          <option value="TN">Tennessee</option>
          <option value="TX">Texas</option>
          <option value="UT">Utah</option>
          <option value="VT">Vermont</option>
          <option value="VA">Virginia</option>
          <option value="WA">Washington</option>
          <option value="WV">West Virginia</option>
          <option value="WI">Wisconsin</option>
          <option value="WY">Wyoming</option>
        </select>				
        <button @click="fetchForecast" class="searchBtn">Search</button>
      </div>
      <h2 class="location">{{location}}</h2>
      <div class="card-wrapper" >
        <div class="card" v-for="(weather, index) in weatherData" :key="weather.list" :class="{active:index===0}">
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
      city_query: '',
      state_query: '',
      location : '',
    }
  },
  methods: {
    fetchForecast(){
      if(!this.state_query == "" && !this.city_query == "" || !this.city_query == null){
        fetch(`${this.forecast_url}forecast?q=${this.city_query},${this.state_query},us&appid=${this.api_key}&units=imperial`)
          .then(res => {
            return res.json();
          }).then(this.storeData);
        }
      else {
        alert('empty inptus');
      }
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
        const temp = Math.round(this.weatherData[x].main.temp),
              tempMin = Math.round(this.weatherData[x].main.temp_min),
              tempMax = Math.round(this.weatherData[x].main.temp_max),
              feelsLike = Math.round(this.weatherData[x].main.feels_like),
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
        this.location = this.city_query + ', ' + this.state_query;
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
    color: #0F110C;
    
    background: linear-gradient(#70e1f5, #ffd194); 
  }

  #app {
    min-height: 100%;
    width: 90%;

    margin: 0 auto;
    padding: 1rem 0;

    display: flex;
    flex-direction: column;
  }
  
  h1, h2 {
    margin-bottom: 0;

    font-family: 'Roboto', sans-serif; 
  }

  h2 {
    margin-top: 3rem;
    margin-bottom: 0;
  }

  p {
    margin: 0;
  }

  section {
    height: 100%;
    width: 100%;

    display: flex; 
    flex-direction: column;
  }

  .form-group {
    max-width: 90%;

    display: flex;
    align-items: stretch;
    flex: 1;
    grid-row: 1 / 2;
    justify-self: center;
    align-self: center;

    margin-top: 1rem;

    font-family: 'lato', sans-serif;
  }

  input {
    width: 30rem;
    height: 2.5rem;

    padding: 1rem;

    border-radius: 0.25rem;
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    border: none;
  }

  button {
    height: 2.5rem;
    width: 5.5rem;

    color: #f5f5f5;
    font-size: 1rem;
    
    background-color: #ff6666;
    border-radius: 0.25rem;
    border-bottom-left-radius: 0;
    border-top-left-radius: 0;
    border: none;;
  }

  .location {
    text-transform: capitalize;
  }

  .card-wrapper {
    width: 100%;  
    height: 100%;

    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;    
    align-self: center;

    color: #0F110C;
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
    background-color: #f5f5f5;
    box-shadow:
    0 2.8px 2.2px rgba(0, 0, 0, 0.034),
    0 6.7px 5.3px rgba(0, 0, 0, 0.048),
    0 12.5px 10px rgba(0, 0, 0, 0.06),
    0 22.3px 17.9px rgba(0, 0, 0, 0.072),
    0 41.8px 33.4px rgba(0, 0, 0, 0.086),
    0 100px 80px rgba(0, 0, 0, 0.12);
    -webkit-box-shadow: 0 2.8px 2.2px rgba(0, 0, 0, 0.034),
    0 6.7px 5.3px rgba(0, 0, 0, 0.048),
    0 12.5px 10px rgba(0, 0, 0, 0.06),
    0 22.3px 17.9px rgba(0, 0, 0, 0.072),
    0 41.8px 33.4px rgba(0, 0, 0, 0.086),
    0 100px 80px rgba(0, 0, 0, 0.12);
    -moz-box-shadow: 0 2.8px 2.2px rgba(0, 0, 0, 0.034),
    0 6.7px 5.3px rgba(0, 0, 0, 0.048),
    0 12.5px 10px rgba(0, 0, 0, 0.06),
    0 22.3px 17.9px rgba(0, 0, 0, 0.072),
    0 41.8px 33.4px rgba(0, 0, 0, 0.086),
    0 100px 80px rgba(0, 0, 0, 0.12); 
     
  }

  .status .temp {
    font-size: 2.5rem;
  }

  .status-icn {
    scale: 2;
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
