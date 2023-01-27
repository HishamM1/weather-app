
<template>
  <main class=" container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-sm"
        v-model="searchQuery" @input="getSearchResults">
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults">
        <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer">{{
          searchResult.place_name
        }}</li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const mapboxAPIKey = 'pk.eyJ1IjoiaGlzaGFtbTEiLCJhIjoiY2xkZG5iNjRuMDVhMzN2bXVsN3lvaHFkeSJ9.qgs1D9Afd6i19FHChjh61A'
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)


const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
      mapboxSearchResults.value = result.data.features;
      console.log(mapboxSearchResults.value);
      return;
    }
    mapboxSearchResults.value = null
  }, 300)

}
</script>