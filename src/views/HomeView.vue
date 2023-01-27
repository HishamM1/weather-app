
<template>
  <main class=" container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-sm"
        v-model="searchQuery" @input="getSearchResults">
      <!-- List of results -->
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults">
        <!-- If there was an error -->
        <p v-if="searchError">Something went wrong, please try again later.</p>
        <!-- If there is no such a country/state -->
        <p v-if="!searchError && mapboxSearchResults.length === 0">No results found.</p>
        <!-- Loop through results -->
        <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer"
          @click="previewCity(searchResult)">
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <p class=" flex justify-center mt-10 text-3xl text-weather-secondary">Loading..</p>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import CityList from "../components/CityList.vue"

const router = useRouter()
const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(', ')
  router.push({
    name: 'city',
    params: {
      city: city,
      state: state.replaceAll(" ", "")
    },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true
    }
  })
}

const mapboxAPIKey = 'pk.eyJ1IjoiaGlzaGFtbTEiLCJhIjoiY2xkZG5iNjRuMDVhMzN2bXVsN3lvaHFkeSJ9.qgs1D9Afd6i19FHChjh61A'
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)
const searchError = ref(null)


const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
        mapboxSearchResults.value = result.data.features;
        console.log(mapboxSearchResults.value);
      } catch {
        searchError.value = true
      }
      return;
    }
    mapboxSearchResults.value = null
  }, 300)

}
</script>