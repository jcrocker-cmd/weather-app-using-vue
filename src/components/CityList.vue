<template>
    <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)"/>
    </div>

    <p v-if="savedCities.length === 0">
        No locations added. To start tracking a location, search in the field above.
    </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue"

// View and Get All Saved Cities
const savedCities = ref([]);
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(
      localStorage.getItem("savedCities")
    );

    const request = [];
    savedCities.value.forEach((city) => {
        request.push(
            axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7775d736060c4fca2d25f2b0f15587ad&units=imperial`)
        );
    });
    const weatherData = await Promise.all(request);
    
    // Flicker Delay
    await new Promise ((res) => setTimeout(res, 1000));

        weatherData.forEach((value,index) => {
        savedCities.value[index].weather = value.data;
    });
  }
}

// Click and go to the saved Cities
await getCities();
const router = useRouter();
const goToCityView = (city) => {
    // URL
    router.push({
        name: 'cityView',
        params: {state: city.state, city: city.city},
        query: {
            id: city.id,
            lat: city.coords.lat,
            lng: city.coords.lng
        }
    });
}
</script>
