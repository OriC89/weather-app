<template>
  <div id="app" :class="typeof weather.current != 'undefined' && weather.current.temp_c > 20 ? 'warm' : ''">

    <main>

      <div class="search-box">
        <input @keypress="fetchWeather" v-model="query" type="text" class="search-bar" placeholder="Search..." />
      </div>

      <div class="weather-wrap" v-if="weather.location != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.location.name }},{{ weather.location.country }}</div>
          <div class="date">{{ weather.location.localtime }}</div>
        </div>
      </div>

      <div class="weather-box" v-if="weather.current != 'undefined'">
        <div class="temp">{{ Math.round(weather.current.temp_c) }}°c</div>
        <div class="weather">{{ weather.current.condition.text }}</div>
      </div>

    </main>

  </div>
</template>

<script>
import { storageService } from './services/storage.service'
const WEATHER_KEY = 'weather'
export default {
  name: 'App',
  data() {
    return {
      api_key: 'f6591847187843b490d73024220307',
      url_base: 'http://api.weatherapi.com/v1/',
      query: '',
      weather: storageService.load(WEATHER_KEY) || {}
    }
  },
  methods: {
    async fetchWeather(ev) {
      if (ev.key === 'Enter') {
        try {
          const apiRequestQuery = `${this.url_base}current.json?key=${this.api_key}&q=${this.query}&aqi=yes`
          const response = await fetch(apiRequestQuery)
          const weather = await response.json()
          this.setResults(weather)
        }
        catch (error) {
          console.log(error)
        }
      }
    },
    setResults(results) {
      this.weather = results
      storageService.save(WEATHER_KEY, this.weather)
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/cold-bg.webp');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url('./assets/summer-bg.webp');
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #131313;
  font-size: 20px;

  border: none;
  outline: none;
  background: none;
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 20px 8px 20px 8px;
  transition: 0.4s;
}

.search-box .search-bar::placeholder {
  color: #131313;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 8px 20px 8px 20px;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 100px;
  font-weight: 900;

  text-shadow: 3px 4px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 2px 4px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #fff;
  font-size: 40px;
  font-weight: 600;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
