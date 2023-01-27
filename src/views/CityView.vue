<template>
    <Suspense>
        <AsyncCityView />
        <template #fallback>
            <p class=" flex justify-center mt-10 text-3xl text-weather-secondary">Loading..</p>
        </template>
    </Suspense>
</template>

<script setup>
import AsyncCityView from "../components/AsyncCityView.vue"
</script>