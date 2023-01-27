<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>

    <p v-if="savedCities.length === 0">
        No locations added. To start tracking a location, search in
        the field above.
    </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";
const savedCities = ref([]);
const getCities = async () => {
    if (localStorage.getItem("savedCities")) {
        savedCities.value = JSON.parse(
            localStorage.getItem("savedCities")
        );
        const requests = [];
        savedCities.value.forEach((city) => {
            if (city.coords.lat === undefined) {
                // remove city from savedCities
                savedCities.value = savedCities.value.filter(
                    (c) => c.id !== city.id
                );
                localStorage.setItem(
                    "savedCities",
                    JSON.stringify(savedCities.value)
                );
                return;
            }
            console.log(city.coords.lat, city.coords.lng);
            requests.push(axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=b2dd904d9b08ce8023c0c053158493f2&units=metric`)
            );
        });
        console.log(requests);
        const weatherData = await Promise.all(requests);
        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        });
        console.log(savedCities.value);
    }
};
await getCities();
const router = useRouter();
const goToCityView = (city) => {
    router.push({
        name: "city",
        params: { state: city.state, city: city.city },
        query: {
            id: city.id,
            lat: city.coords.lat,
            lng: city.coords.lng,
        },
    });
};
</script>