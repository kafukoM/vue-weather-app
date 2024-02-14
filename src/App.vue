<template>
  <main>
    <div class="search-box">
      <input type="text" class="search-bar" placeholder="Search..." v-model="query" @keyup.enter="getWeatherData">
    </div>

    <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
      <div class="location-box">
        <div class="location">{{ weather.name }}, {{ weather.sys?.country }}</div>
        <div class="date">Wednesday 7 February 2024</div> <!-- Consider dynamically setting the date -->
      </div>

      <div class="weather-box">
        <div class="temp">{{ Math.round(weather.main.temp) }}°C</div> <!-- Assuming you're getting temp in Kelvin -->
        <div class="weather">{{ weather.weather[0].main }}</div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      api_key: '09c87b474e8275419b70100439438bba',
      url_base_weather: 'https://api.openweathermap.org/data/2.5/weather',
      url_base_geo: 'http://api.openweathermap.org/geo/1.0/direct',
      query: '',
      weather: {}
    }
  },
  methods: {
    async getWeatherData() {
      try {
        const geoResponse = await fetch(`${this.url_base_geo}?q=${this.query}&appid=${this.api_key}`);
        const geoJson = await geoResponse.json();
        if (geoJson.length > 0) {
          const latitude = geoJson[0].lat;
          const longitude = geoJson[0].lon;

          const weatherResponse = await fetch(`${this.url_base_weather}?lat=${latitude}&lon=${longitude}&units=metric&appid=${this.api_key}`);
          this.weather = await weatherResponse.json();

          console.log(this.weather); // For debugging/verification
        } else {
          console.error("Location not found");
        }
      } catch (error) {
        console.error("Failed to fetch weather data:", error);
      }
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
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url('./assets/warm-bg.jpg');
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
  
  color: #313131;
  font-size: 20px;

  appearance: none;
  border:none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
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
  color: #FFF;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color:rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
