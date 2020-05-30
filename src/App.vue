<template>
  <div id="app">
    <main>
      <!-- Search Box -->
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <!-- Weather Module -->
      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{weather.name}}</div>
          <div class="date" v-bind="date">{{date}}</div>
        </div>
        <div class="weather-box">
          <div
            class="temperature"
          >{{Math.round(weather.main.temp * 10)/10}}° + {{weather.weather[0].main}}</div>
          <div style="display:none" class="weather">{{weather.weather[0].main}}</div>
        </div>
      </div>

      <!-- No information block -->
      <div
        class="no-information"
        v-if="typeof weather.main == 'undefined'"
        @click="focusSearch"
      >Search for a known city at the top... ⬆️</div>
    </main>
    <footer id="footer">Made by _ with _. Share it!</footer>
  </div>
</template>

<script>
import moment from "moment";
import keys from "../keys";

function currentDate() {
  return moment().format("dddd[, ] Do MMM YYYY [@] hh:mm");
}
/** this method gets the weather */
function fetchWeather(e) {
  if (e.key == "Enter") {
    // create call string
    let call = `${this.url_base}weather?q=${this.query}&units=metric&appid=${this.api_key}`;

    // execute async call and update ui
    fetch(call)
      .then(response => {
        return response.json();
      })
      .then(data => {
        // the weather information is automatically updated by vue
        this.weather = data;

        this.date = currentDate();
      })
      .then(() => {
        // reset input field + unfocus (blur) the search bar
        let searchBar = document.querySelector(".search-bar");
        searchBar.value = "";
        searchBar.blur();
      });
  }
}

function focusSearch() {
  document.querySelector(".search-bar").focus();
}
export default {
  name: "App",
  data: function data() {
    return {
      api_key: keys.openweather.api_key,
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      date: ""
    };
  },
  methods: {
    fetchWeather,
    focusSearch
  }
};
</script>

<style>
:root {
  --border-radius: 8px;
  --trans-speed: all 0.3s ease;
  --hover-bg-color: rgba(255, 255, 255, 0.75);
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Avenir Next", "Helvetica";
}

#app {
  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: var(--trans-speed);
}

main {
  min-height: 100vh;
  padding: 2rem;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 8px 15px;
  color: #555555;
  font-size: 16px;

  /** reset default styles */
  border: none;
  appearance: none;
  outline: none;
  background: none;

  /* set background syles & transition */
  background-color: rgba(255, 255, 255, 0.4);
  /* box-shadow: 0 2px 10px rgba(0, 0, 0, 0.55); */
  transition: var(--trans-speed);

  font-family: "Avenir Next", "Helvetica";
  border-radius: var(--border-radius);
}

.search-box .search-bar:hover {
  background-color: var(--hover-bg-color);
  color: rgba(0, 0, 0, 0.9);
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.8);
  box-shadow: 0 20px 20px rgba(0, 0, 0, 0.55);
  padding: 15px 10px;
  font-size: 20px;
}

.location-box {
  text-align: center;
}

.location-box .location {
  color: white;
  font-size: 32px;
  font-weight: 500;
  text-shadow: 1px 2px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: white;
  font-size: 20px;
  font-weight: 300;
}

.weather-box {
  text-align: center;
}

.weather-box .temperature {
  display: inline-block;
  padding: 10px 30px;
  width: 100%;
  color: white;
  font-size: 10vw;
  font-weight: 500;
  text-shadow: 2px 4px rgba(0, 0, 0, 0.35);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: var(--border-radius);
  margin: 1rem 0;
  transition: var(--trans-speed);

  /* box-shadow: 3px 6px rgba(0, 0, 0, 0.25); */
}

.weather-box .temperature:hover {
  background-color: var(--hover-bg-color);
  color: rgba(0, 0, 0, 0.9);
}

.weather-box .weather {
  color: white;
  font-size: 32px;
  font-weight: 400;
  text-shadow: 2px 4px rgba(0, 0, 0, 0.25);
}

.no-information {
  display: inline-block;
  padding: 10px 30px;
  width: 100%;
  color: white;
  /* see https://www.mediaevent.de/css-fluid-font-size/*/
  font-size: calc(18px + (28 - 18) * (100vw - 400px) / (100vw - 400));
  font-weight: 300;
  text-shadow: 0.1px 0.4px rgba(0, 0, 0, 0.35);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: var(--border-radius);
  margin: 1rem 0;
  transition: var(--trans-speed);
}

.no-information:hover {
  background-color: var(--hover-bg-color);
  color: rgba(0, 0, 0, 0.9);
}

#footer {
  position: fixed;
  color: white;
  height: 50px;
  background-color: rgba(255, 255, 255, 0.35);
  bottom: 0px;
  left: 0px;
  right: 0px;
  margin-bottom: 0px;
  padding: 5px;
  transition: var(--trans-speed);
  text-align: center;
}

#footer:hover {
  background-color: var(--hover-bg-color);
  color: rgba(0, 0, 0, 0.9);
}
</style>
