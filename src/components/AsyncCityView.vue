<template>
  <div></div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from 'vue-router'

const openWeatherAPIKey = process.env.VUE_APP_OPENWEATHER_KEY
const route = useRoute()

const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${openWeatherAPIKey}&units=imperial`
    )
    //calc current date & time
    const localOffset = new Date().getTimeZoneOffset() * 60000
    const utc = weatherData.data.current.dt * 1000 + localOffset
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset

    //calc hourly weather offset
    weatherData.data.hourly.forEach(hour => {
      const utc = hour.dt * 1000 + localOffset
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset
    })

    return weatherData
  } catch (error) {
    console.log(error)
  }
}

const weatherData = await getWeatherData()
console.log(weatherData)
</script>
