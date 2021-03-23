<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Search..."
          v-model="search_query"
          @keypress="weatherQuery"
        />
      </div>

      <div class="weather-wrapper" v-if="typeof weather.main !='undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box">
          <div class="temp">{{Math.round(weather.main.temp)}}°</div>
          <div class="weather">{{weather.weather[0].main}}</div>
          <div class="weather-description">{{weather.weather[0].description}}</div>
        </div>
        <div class="forecast-wrapper" 
        v-if="typeof forecast_one.main != 'undefined' &&
        forecast_two.main != 'undefined' &&
        forecast_three.main != 'undefined' &&
        forecast_four.main != 'undefined'
        ">
          <span class="forecast-title">Today's Forecast</span>
          <div class="forecast-grid">
            <div class="forecast">
              <span class="weather-time">{{extractTime(forecast_one.dt_txt)}}</span>
              <div class="forecast-box">
                <div class="forecast-temp">{{Math.round(forecast_one.main.temp)}}°</div>
                <div class="forecast-weather">{{forecast_one.weather[0].main}}</div>
              </div>
            </div>
            <div class="forecast">
              <span class="weather-time">{{extractTime(forecast_two.dt_txt)}}</span>
              <div class="forecast-box">
                <div class="forecast-temp">{{Math.round(forecast_two.main.temp)}}°</div>
                <div class="forecast-weather">{{forecast_two.weather[0].main}}</div>
              </div>
            </div>
            <div class="forecast">
              <span class="weather-time">{{extractTime(forecast_three.dt_txt)}}</span>
              <div class="forecast-box">
                <div class="forecast-temp">{{Math.round(forecast_three.main.temp)}}°</div>
                <div class="forecast-weather">{{forecast_three.weather[0].main}}</div>
              </div>
            </div>
            <div class="forecast">
              <span class="weather-time">{{extractTime(forecast_four.dt_txt)}}</span>
              <div class="forecast-box">
                <div class="forecast-temp">{{Math.round(forecast_four.main.temp)}}°</div>
                <div class="forecast-weather">{{forecast_three.weather[0].main}}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="footer">
        <div class="provided-by">Weather provided by <a href="https://openweathermap.org/">Open Weather</a></div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      api_key: '883e6bce78539b8d2e11161be7a6b376',
      api_url: 'https://api.openweathermap.org/data/2.5/',
      weather: {},
      forecast_one: {},
      forecast_two: {},
      forecast_three: {},
      forecast_four: {},
    }
  },
  methods: {
    weatherQuery (e) {
      if (e.key == "Enter") {
        fetch(`${this.api_url}weather?q=${this.search_query}&units=metric&APPID=${this.api_key}`)
          .then(data => {
            return data.json()
          }).then(this.queryResults);
      }
    },
    queryResults (results) {
      this.weather = results;
      fetch(`${this.api_url}/forecast?id=${results.id}&units=metric&APPID=${this.api_key}`).then(res => {
        return res.json()
      }).then(this.queryForecast);
    },
    queryForecast (results) {
      this.forecast_one = results["list"][0];
      this.forecast_two = results["list"][1];
      this.forecast_three = results["list"][2];
      this.forecast_four = results["list"][3];
      console.log(JSON.stringify(results))
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
    extractTime(string) {
      let date = new Date(string);
      let hour = date.getHours();
      let minute = date.getMinutes();

      if (hour < 10) {
        hour += '0';
      }

      if (minute < 10) {
        minute += '0';
      }

      return `${hour}:${minute}`;
    }
  }
}
</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Roboto', sans-serif;
}

#app {
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: .4s;
}

#app.warm {
  background-image: url('./assets/warm-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
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
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0,0,0,0.25);
  background-color: rgba(255, 255, 255, 0.377);
  border-bottom: 2px solid rgba(255, 255, 255, 0);
  transition: .4s;
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.158);
  box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
  color: white;
  border-bottom: 2px solid rgba(255, 255, 255, 0.61);
  border-radius: 0;
  text-shadow: 1px 3px rgba(0,0,0,0.25);
}

.location-box .location {
  color: white;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0,0,0,0.25);
}

.location-box .date {
  color: white;
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
  color: white;
  font-size: 100px;
  font-weight: 700;
  text-shadow: 3px 6px rgba(0,0,0,0.25);
  margin: 10px 0;
}

.weather-box .weather {
  color: white;
  font-size: 38px;
  font-weight: bold;
  text-shadow: 3px 6px rgba(0,0,0,0.25);
}

.weather-box .weather-description {
  color: white;
  font-style: italic;
}

.forecast-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  text-align: center;
}

hr {
  border-bottom: 2px solid rgba(255, 255, 255, 0.671);
  margin: 18px 0;
}

.forecast-wrapper .forecast-title {
  display: block;
  color: white;
  text-align: center;
  font-size: 25px;
  font-weight: 700;
  margin: 25px 0 20px 0;
}

.forecast .weather-time {
  color: white;
  font-weight: 500;
}

.forecast-box {
  background-color: rgba(255, 255, 255, 0.25);
  border: 1px solid rgba(255, 255, 255, 0.25);
  margin: 5px 3px;
  border-radius: 5px;
  box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
}

.forecast-box .forecast-temp {
  color: white;
  font-size: 24px;
  font-weight: 500;
}

.forecast-box .forecast-weather {
  color: white;
  font-size: 14px;
  font-style: italic;
}

.footer {
  position: fixed;
  width: 90vw;
  bottom: 1rem;
}

.provided-by {
  display: block;
  margin: 0 auto;
  text-align: center;
  color: rgb(204, 204, 204);
}

.provided-by a {
  color: white;
  text-decoration: none;
}

</style>
