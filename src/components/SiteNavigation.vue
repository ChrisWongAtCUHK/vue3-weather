<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
      class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3 flex-1">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">The Local Weather</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="toggleModal"
        ></i>
        <i v-if="route.query.preview"
          class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="addCity"
        ></i>
      </div>

      <BaseModel :modalActive="modalActive" @close-modal="toggleModal">
        <div class="text-black">
          <h1 class="text-2xl mb-1">About:</h1>
          <p class="mb-4">
            The Local Weather allows you to track the current future weather of
            cities of your choosing.
          </p>
          <h1 class="text-2xl mb-1">How it works:</h1>
          <ol class="list-decimal list-inside mb-4">
            <li>Search for your city by entering name into the search bar.</li>
            <li>
              Select a city within the results, this will take you to the
              current weather for your selection.
            </li>
            <li>
              Track the city by clicking on the "+" icon in the top right. This
              will save the city to view at a later time on the home page.
            </li>
          </ol>
          <h1 class="text-2xl mb-1">Removing a city:</h1>
          <p class="mb-4">
            If you no later wish to track a city, simply select the city within
            the home page. At the bottom of the page, there will be an option to
            delete the city.
          </p>
        </div>
      </BaseModel>
    </nav>
  </header>
</template>

<script setup>
import { ref } from 'vue'
import { uid } from 'uid'
import { RouterLink, useRoute, useRouter } from 'vue-router'
import BaseModel from './BaseModel.vue'

const route = useRoute()
const router = useRouter()
const modalActive = ref(null)
const savedCities = ref([])

const toggleModal = () => {
  modalActive.value = !modalActive.value
}

const addCity = () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
  }
  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lon: route.query.lon,
      lat: route.query.lat,
    },
  }

  savedCities.value.push(locationObj)
  localStorage.setItem('savedCities', JSON.stringify(savedCities.value))

  let query = Object.assign({}, route.query)
  delete query.preview
  router.replace({ query })
}
</script>
