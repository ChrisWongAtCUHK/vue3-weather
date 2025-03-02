<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>
  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import CityCard from './CityCard.vue'

const router = useRouter()
const openweathermapAPIKey = import.meta.env.VITE_OPENWEATHERMAP_API_KEY
const savedCities = ref([])
const getCities = async () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'))

    const requests = []
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lon}&appid=${openweathermapAPIKey}&units=imperial`
        )
      )
    })

    const weatherData = await Promise.all(requests)
    weatherData.forEach((weather, index) => {
      savedCities.value[index].weather = weather.data
    })
  }
}

const goToCityView = (city) => {
  router.push({
    name: 'cityView',
    params: {
      state: city.state,
      city: city.city
    },
    query: {
      lat: city.coords.lat,
      lon: city.coords.lon,
    }
  })
}

await getCities()
</script>
