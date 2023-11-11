<script>
export default {
  data() {
    return {
      cityName: 'Bangkok',
      weatherData: [],
      main: '',
      description: '',
      temp: '',
      formattedDate: '',
      humidity: '',
      visibility: '',
      pressure: '',
      windspeed: '',
      sunset: '',
      sunrise: '',
      name: ''
    }
  },
  methods: {
    async getCoordinatesFromCity() {
      const cityName = this.cityName
      const geocodingUrl = `https://api.openweathermap.org/geo/1.0/direct?q=${cityName}&limit=1&appid=5b95e06eb295b0c21120e913e524eefb`
      try {
        const geocodingRes = await fetch(geocodingUrl)
        const geoData = await geocodingRes.json()

        const lat = geoData[0].lat.toString()
        const lon = geoData[0].lon.toString()

        console.log(`${cityName} latitude: `, lat)
        console.log(`${cityName} longitude: `, lon)

        const weatherUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=5b95e06eb295b0c21120e913e524eefb`
        try {
          const weatherRes = await fetch(weatherUrl)
          const weatherData = await weatherRes.json()

          console.log(`Current ${cityName} Weather`)
          console.log(weatherData)
          console.log(weatherData.timezone)
          console.log(weatherData.weather[0].main)
          this.weatherData = [weatherData]

          const temp = weatherData.main.feels_like - 273.15
          this.temp = temp.toFixed(1)
          const humidity = weatherData.main.humidity
          this.humidity = humidity
          const pressure = weatherData.main.pressure
          this.pressure = pressure
          const visibility = weatherData.visibility / 1000
          this.visibility = visibility
          const windspeed = weatherData.wind.speed * 3.6
          this.windspeed = windspeed.toFixed(2)
          const name = weatherData.name
          this.name = name

          const sunriseDate = new Date(weatherData.sys.sunrise * 1000)
          const sunrise = sunriseDate.toLocaleTimeString('en-US', {
            hour: '2-digit',
            minute: '2-digit'
          })
          this.sunrise = sunrise
          console.log(sunrise)

          const sunsetDate = new Date(weatherData.sys.sunset * 1000)
          const sunset = sunsetDate.toLocaleTimeString('en-US', {
            hour: '2-digit',
            minute: '2-digit'
          })
          this.sunset = sunset
          console.log(sunset)

          const timezoneOffsetMs = weatherData.timezone * 1000
          const currentTime = new Date()
          const targetTime = new Date(currentTime.getTime() + timezoneOffsetMs)
          const targetTimeZone = 'Europe/London'

          const formattedDate = targetTime.toLocaleString('en-US', {
            timeZone: targetTimeZone,
            weekday: 'long',
            hour: 'numeric',
            minute: 'numeric',
            second: 'numeric',
            hour12: true
          })

          console.log(formattedDate)
          this.formattedDate = formattedDate

          const main = weatherData.weather[0].main.toLowerCase()
          const description = weatherData.weather[0].description.toLowerCase()
          this.main = main
          this.description = description

          console.log(main, description)
        } catch (error) {
          console.error(error)
          throw error
        }
      } catch (error) {
        console.error(error)
        throw error
      }
      this.cityName = ''
    },
    sendValue() {
      // Use this.searchValue to access the input value in your methods
      console.log('Input Value:', this.searchValue)
      // You can also perform other actions with this.searchValue here
    },
    displayWeather() {
      this.getCoordinatesFromCity(this.cityName)
    }
  },
  mounted() {
    this.displayWeather();
  }
}
</script>

<template>
  <div class="weather-wrapper">
    <div class="navb-weather">
      <div class="logo-weather">
        <div class="logo-icon">
          <font-awesome-icon icon="rainbow" style="color: #fff; height: 100%; width: 100%" />
        </div>
        <div class="logo-name">
          <h1>MiWeather</h1>
        </div>
      </div>
      <div class="search-bar">
        <input
          type="text"
          v-model="cityName"
          @keydown.enter="getCoordinatesFromCity"
          placeholder="Enter City..."
        />
        <button type="submit" @click="getCoordinatesFromCity">
          <font-awesome-icon icon="magnifying-glass" style="color: #282c34" beat />
        </button>
      </div>
    </div>
    <div class="weather-detail">
      <div class="container">
        <div class="row">
          <div class="col">
            <!-- First Column with 1 row -->
            <div class="row">
              <div class="col">
                <div>
                  <div v-if="weatherData.length > 0">
                    <div v-if="main.includes('cloud') || description.includes('cloud')">
                      <img src="../assets/logo/cloudy.png" alt="cloudy" />
                    </div>
                    <div v-else-if="main.includes('rain') || description.includes('rain')">
                      <img src="../assets/logo/rain.png" alt="rain" />
                    </div>
                    <div
                      v-else-if="
                        main.includes('thunderstorm') || description.includes('thunderstorm')
                      "
                    >
                      <img src="../assets/logo/thunderstorm.png" alt="thunderstorm" />
                    </div>
                    <div v-else-if="main.includes('drizzle') || description.includes('drizzle')">
                      <img src="../assets/logo/weather.png" alt="drizzle" />
                    </div>
                    <div v-else-if="main.includes('snow') || description.includes('snow')">
                      <img src="../assets/logo/snowing.png" alt="snow" />
                    </div>
                    <div v-else-if="main.includes('clear') || description.includes('clear')">
                      <img src="../assets/logo/sun.png" alt="clear" />
                    </div>
                    <div v-else>
                      <img src="../assets/logo/smog.png" alt="smog" />
                    </div>
                    <h2>{{ temp }} °C</h2>
                    <div>
                      <h4 v-if="weatherData[0].weather && weatherData[0].weather.length > 0">
                        {{ weatherData[0].weather[0].main }}
                      </h4>
                      <p v-if="weatherData[0].weather && weatherData[0].weather.length > 0">
                        ( {{ weatherData[0].weather[0].description }} )
                      </p>
                    </div>
                    <div class="d-flex align-items-center justify-content-center">
                      <font-awesome-icon
                        icon="location-dot"
                        style="
                          color: #282c34;
                          height: 1.8rem;
                          width: 1.8rem;
                          margin-bottom: 5px;
                          margin-right: 5px;
                        "
                      />
                      <h2>{{ name }}</h2>
                    </div>
                    <div class="d-flex align-items-center justify-content-center">
                      <font-awesome-icon
                        icon="clock"
                        style="
                          color: #282c34;
                          height: 1.4rem;
                          width: 1.4rem;
                          margin-bottom: 8px;
                          margin-right: 5px;
                        "
                      />
                      <h4>{{ formattedDate }}</h4>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="col" style="display: grid; margin-left: 20px; margin-right: 20px">
            <!-- Second Column with 3 rows -->
            <div class="row" style="padding-bottom: 20px">
              <div class="col">
                <div class="d-flex">
                  <font-awesome-icon
                    icon="bars-staggered"
                    style="color: #454545; height: 5rem; width: 5rem; margin-right: 20px"
                    fade
                  />
                  <div>
                    <h5>Visibility</h5>
                    <p>{{ visibility }} km</p>
                  </div>
                </div>
              </div>
            </div>
            <div class="row" style="padding-bottom: 20px">
              <div class="col">
                <div class="d-flex">
                  <font-awesome-icon
                    icon="weight-scale"
                    style="color: #d83f31; height: 5rem; width: 5rem; margin-right: 20px"
                    shake
                  />
                  <div>
                    <h5>Pressure</h5>
                    <p>{{ pressure }} hPa</p>
                  </div>
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col">
                <div class="d-flex">
                  <font-awesome-icon
                    icon="sun"
                    style="color: #ee9322; height: 5rem; width: 5rem; margin-right: 20px"
                    spin
                  />
                  <div>
                    <h5>Sunrise</h5>
                    <p>{{ sunrise }}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="col" style="display: grid">
            <!-- Third Column with 3 rows -->
            <div class="row" style="padding-bottom: 20px">
              <div class="col">
                <div class="d-flex">
                  <font-awesome-icon
                    icon="droplet"
                    style="color: #3876bf; height: 5rem; width: 5rem; margin-right: 20px"
                    bounce
                  />
                  <div>
                    <h5>Humidity</h5>
                    <p>{{ humidity }} %</p>
                  </div>
                </div>
              </div>
            </div>
            <div class="row" style="padding-bottom: 20px">
              <div class="col">
                <div class="d-flex">
                  <font-awesome-icon
                    icon="wind"
                    style="color: #219c90; height: 5rem; width: 5rem; margin-right: 20px"
                    beat
                  />
                  <div>
                    <h5>Wind Speed</h5>
                    <p>{{ windspeed }} km/h</p>
                  </div>
                </div>
              </div>
            </div>
            <div class="row">
              <div class="col">
                <div class="d-flex">
                  <font-awesome-icon
                    icon="moon"
                    style="color: #e9b824; height: 5rem; width: 5rem; margin-right: 20px"
                    flip
                  />
                  <div>
                    <h5>Sunset</h5>
                    <p>{{ sunset }}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import url('../assets/scss/_header.scss');
</style>
