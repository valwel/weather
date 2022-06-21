<template>
  <div class="container">
    <h1>Погода в ростове-на-Дону</h1>
    <div v-if="current" class="card">
      <div class="card__time">Сейчас {{ current.time }}</div>
      <div class="card__temp">
        <div class="card__temp-value">{{ current.temp }}</div>
        <div class="card__temp-icon">
          <img :src="`https://openweathermap.org/img/wn/${current.icon}@2x.png`" alt="">
          <div class="card__temp-description">{{ current.description }}</div>
        </div>
      </div>
    </div>

    <h2>Прогноз погоды</h2>

    <div class="forecast">
      <div v-for="item in forecast" class="card card--small">
        <div class="card__time">{{ getDateString(item.dt_txt) }}</div>
        <div class="card__temp">
          <div class="card__temp-value">{{ getWeatherValue(item.main.temp) }}</div>
          <div class="card__temp-icon">
            <img :src="`https://openweathermap.org/img/wn/${item.weather[0].icon}@2x.png`" alt="">
            <div class="card__temp-description">{{ item.weather[0].description }}</div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      forecast: [],
      current: null,
      apiOptions: {
        lon: 39.7139,
        lat: 47.2364,
        units: 'metric',
        lang: 'ru',
        weatherApiKey: 'c88f0e14775ebe6af4f4fa97503fcccf',
      },
    }
  },

  methods: {
    getWeatherValue(temp) {
      const rounded = Math.round(temp)
      return rounded > 0 ? `+ ${rounded}℃` : `${rounded}℃`
    },

    getDateString(dateString) {
      return new Date(dateString).toLocaleString('ru-RU')
    },

    setCurrent(currentWeather) {
      return this.current = currentWeather
    },
    setForecast(forecast) {
      return this.forecast = forecast
    },

    async show() {
      const f = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${this.apiOptions.lat}&lon=${this.apiOptions.lon}&units=${this.apiOptions.units}&lang=${this.apiOptions.lang}&appid=${this.apiOptions.weatherApiKey}`);
      const data = await f.json();

      const foo = await fetch(`https://api.openweathermap.org/data/2.5/forecast?lat=${this.apiOptions.lat}&lon=${this.apiOptions.lon}&cnt=5&&units=${this.apiOptions.units}&lang=${this.apiOptions.lang}&appid=${this.apiOptions.weatherApiKey}`);
      const dataForecast = await foo.json();

      this.setCurrent(
        {
          temp: this.getWeatherValue(data.main.temp),
          icon: data.weather[0].icon,
          description: data.weather[0].description,
          time: new Date().toLocaleTimeString('ru-RU', { hour: '2-digit', minute: '2-digit' })
        }
      );
      this.setForecast(dataForecast.list);

      setInterval( () => {
        this.show()
      }, 60 * 1000)
    },

  },
  mounted() {
    this.show();
  }
}
</script>

<style lang="scss">
#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
}

html {
  background-color: #eaeaea;
}

body {
  margin: 0;
  padding: 0;
}

h1 {
  @media (max-width: 750px) {
    font-size: 26px;
  }
}

.container {
  width: 100%;
  max-width: 1300px;
  margin: 0 auto;

  @media (max-width: 768px) {
    max-width: 100%;
    padding: 0 10px;
  }
}

.forecast {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-left: -15px;
  margin-top: -15px;

  .card {

    margin-left: 15px;
    margin-top: 15px;

  }

  @media (max-width: 768px) {
    flex-direction: column;
    margin: 0;

    .card {
      margin: 0 0 15px 0;
    }
  }
}

.card {
  border-radius: 15px;
  background-color: #ffffff;
  box-shadow: 5px 5px 10px;
  padding: 30px;
  display: inline-flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  &__time {
    text-align: left;
    font-size: 20px;
    text-align: center;

    @media (max-width: 750px) {
      font-size: 16px;
    }
  }

  &__temp {
    display: flex;
    align-items: center;

    @media (max-width: 750px) {
      flex-direction: column;
    }
  }

  &__temp-value {
    font-size: 60px;

    @media (max-width: 750px) {
      font-size: 30px;
    }
  }


  &--small {
    min-width: 300px;

    .card {
      &__temp-value {
        font-size: 40px;
      }
    }
  }
}
</style>
