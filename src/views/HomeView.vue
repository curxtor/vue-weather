<template>
  <div class="home">
    <div class="header">
      <div class="search_city" :class="cityInputDropdown.length > 0 ? 'dropdown' : ''">
        <i class="pi pi-search" style="color: #788294"></i>
        <input type="text" @input="searchCity" v-model="cityInput" placeholder="Search city..." />
        <Transition name="fade">
          <div class="city_dropdown" v-if="isCityDropDown">
            <div class="city_dropdown_item" v-for="(item, index) in cityInputDropdown" :key="index" @click="changeCity(item.city)">
              <p>{{ item.city }}</p>
            </div>
          </div>
        </Transition>
      </div>
      <div class="degrees_switch">
        <p class="selected_degree">°С</p>
        <p>°F</p>
      </div>
    </div>
    <div class="main_block" v-if="resWeather">
      <div class="main_info">
        <div class="city_block">
          <div class="city_block_header">
            <p class="degree_number">{{ Math.round(resWeather.current.temperature_2m) }}°</p>
            <div class="city_info">
              <p class="city_info_name">{{ cityInfo.city }}, <br />{{ cityInfo.country }}</p>
              <p>{{ new Date(resWeather.current.time).toLocaleDateString("ru-RU") }} {{ new Date(resWeather.current.time).toLocaleTimeString("ru-RU").slice(0, -3) }}</p>
            </div>
          </div>
          <div class="city_block_info">
            <div class="city_block_left_info">
              <p style="display: flex; align-items: center"><img :src="require(`../assets/icons/${resWeather.current.weather_code.key}.svg`)" />{{ resWeather.current.weather_code.description }}</p>
              <p style="font-size: 12px; color: #788294">Feel like: {{ Math.round(resWeather.current.apparent_temperature) }} °С</p>
            </div>
            <div class="city_block_right_info">
              <p style="display: flex; align-items: center"><img src="../assets/icons/wind.svg" width="40" />{{ resWeather.current.wind_speed_10m }} m/s</p>
            </div>
          </div>
        </div>
        <div class="time_forecast">
          <div class="time_forecast_block" v-for="(item, index) in forecast" :key="item.time">
            <p style="font-size: 24px">{{ new Date(index).toLocaleTimeString("ru-RU", { timeStyle: "short" }) }}</p>
            <hr />
            <img :src="require(`../assets/icons/${item.weather_code.key}.svg`)" />
            <p>{{ item.weather_code.description }}</p>
            <p style="font-size: 32px; font-weight: 600; margin-top: 25px">{{ Math.round(item.temperature_2m) }}°</p>
          </div>
        </div>
      </div>
      <div class="additional_info">
        <div class="cities_info">
          <p>Other random cities</p>
          <div class="city_block">
            <div class="city_block_header">
              <p class="degree_number">{{ Math.round(resWeather.current.temperature_2m) }}°</p>
              <div class="city_info">
                <p class="city_info_name">{{ cityInfo.city }}</p>
                <p>{{ new Date(resWeather.current.time).toLocaleDateString("ru-RU") }} {{ new Date(resWeather.current.time).toLocaleTimeString("ru-RU").slice(0, -3) }}</p>
              </div>
            </div>
            <div class="city_block_info">
              <div class="city_block_left_info">
                <p style="display: flex; align-items: center"><img :src="require(`../assets/icons/${resWeather.current.weather_code.key}.svg`)" />{{ resWeather.current.weather_code.description }}</p>
                <p style="font-size: 12px; color: #788294">Feel like: {{ Math.round(resWeather.current.apparent_temperature) }} °С</p>
              </div>
              <div class="city_block_right_info">
                <p>5.14 m/s</p>
                <p>-1° to 3°</p>
              </div>
            </div>
          </div>
        </div>
        <div class="5-day-forecast">
          
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from "axios"
  import _ from "lodash"
  export default {
    name: "HomeView",
    data() {
      return {
        cityInfo: null,
        forecast: [],
        resWeather: null,
        cityInput: "",
        cityInputDropdown: [],
        isCityDropDown: false,
      }
    },
    methods: {
      getCityInfo(city) {
        axios
          .get(`${process.env.VUE_APP_SERVER_ADDRESS}/city?city=${city}`)
          .then((res) => {
            console.log(res)
            this.resWeather = res.data.resWeather
            let time = new Date(res.data.resWeather.current.time)
            let hours = new Date(time).getHours()
            time.setUTCHours(hours, 0)
            time = time.toISOString()
            this.cityInfo = res.data.cityInfo
            const trimmedData = Object.fromEntries(Object.entries(res.data.forecast).filter(([key]) => key >= time.slice(0, -8)))
            this.forecast = trimmedData
          })
          .catch((err) => {
            console.log(err)
          })
      },
      searchCity: _.debounce(function () {
        if (this.cityInput.length > 1) {
          axios
            .post(`${process.env.VUE_APP_SERVER_ADDRESS}/search_city`, { query: this.cityInput })
            .then((res) => {
              this.cityInputDropdown = res.data
              this.cityInputDropdown.length > 0 ? (this.isCityDropDown = true) : (this.isCityDropDown = false)
            })
            .catch((err) => {
              console.log(err)
            })
        } else {
          this.cityInputDropdown = []
          this.isCityDropDown = false
        }
      }),
      changeCity(city) {
        this.cityInput = city
        this.isCityDropDown = false
        this.cityInputDropdown = []
        this.getCityInfo(city)
      },
    },
    mounted() {
      this.getCityInfo("Novosibirsk")
    },
  }
</script>

<style scoped>
  .home {
    padding: 1.5% 3%;
    display: flex;
    flex-direction: column;
    gap: 2vh;
  }
  .header {
    display: flex;
    justify-content: space-between;
  }
  .search_city {
    background: #20293a;
    border-radius: 20px;
    padding: 10px;
    display: flex;
    align-items: center;
    gap: 7px;
    position: relative;
  }
  .search_city.dropdown {
    border-radius: 20px 20px 0 0;
  }
  .city_dropdown {
    position: absolute;
    top: 100%;
    left: 0;
    background-color: #20293a;
    border-radius: 0 0 20px 20px;
    width: 100%;
    max-height: 200px;
    overflow-y: auto;
    z-index: 10;
    display: flex;
    flex-direction: column;
    gap: 5px;
    padding: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    scrollbar-width: thin;
    scrollbar-color: #788294 transparent;
  }
  .city_dropdown_item {
    width: 100%;
    padding: 5px;
    transition: 0.2s all;
  }
  .city_dropdown_item:hover {
    background-color: #788294;
    color: white;
    cursor: pointer;
  }
  .search_city input {
    background: transparent;
    outline: none;
    border: none;
    color: white;
    font-size: 16px;
  }
  .search_city input::placeholder {
    color: #6c7d7f;
  }
  .degrees_switch {
    display: flex;
    align-items: center;
    gap: 10px;
    background-color: #20293a;
    border-radius: 20px;
    padding: 0 10px;
  }
  .selected_degree {
    background-color: white;
    height: 100%;
    border-radius: 20px;
    color: #232635;
    display: flex;
    align-items: center;
  }
  .main_block {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 3vh;
  }
  .main_info,
  .additional_info {
    display: flex;
    width: 100%;
    gap: 3vw;
  }
  .city_block {
    display: flex;
    flex-direction: column;
    flex: 1;
    background-color: #20293a;
    border-radius: 20px;
    padding: 1vw;
  }
  .city_block_header {
    display: flex;
    justify-content: space-between;
    flex: 1;
  }
  .degree_number {
    font-size: 4rem;
    font-weight: bold;
  }
  .city_info {
    text-align: right;
    display: flex;
    flex-direction: column;
    gap: 5px;
  }
  .city_info_name {
    font-weight: 600;
    font-size: 36px;
  }
  .city_block_info {
    display: flex;
    justify-content: space-between;
  }
  .city_block_left_info {
    text-align: left;
  }
  .city_block_right_info {
    text-align: right;
  }
  .time_forecast {
    flex: 4;
    display: flex;
    grid-template-columns: repeat(9, 1fr);
    gap: 20px;
    overflow-x: auto;
    scroll-behavior: smooth;
    scrollbar-width: thin;
    scrollbar-color: #788294 transparent;
  }

  .time_forecast_block {
    background-color: #20293a;
    border-radius: 20px;
    padding: 1%;
    display: flex;
    flex-direction: column;
    align-items: center;
    min-width: 5vw;
  }
  .time_forecast_block img {
    width: 60px;
  }
  .time_forecast_block hr {
    width: 100%;
    border: 1px solid #788294;
  }
</style>
