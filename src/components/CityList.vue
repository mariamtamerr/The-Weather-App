<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";

const router = useRouter();
const savedCities = ref([]);

const getCities = async () => {
  // first get all saved items if any
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }

  // make requests to get each saved city
  const requests = [];

  savedCities.value.forEach((city) => {
    requests.push(
      axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
      )
    );
  });

  //  array & promise to wait for all city requests
  const weatherData = await Promise.all(requests);

  //   Flicker Delay for skeleton or better UI when loading
  await new Promise((res) => setTimeout(res, 1000));

  // match the data for ecah city in weatherData array with nafsaha in savedCities array
  weatherData.forEach((value, index) => {
    savedCities.value[index].weather = value.data; //weather da new property , data da el gay mn api
  });
};

// awaitttttt
await getCities();

// go to city when clicking on it
const goToCityView = (city) => {
  router.push({
    name: "CityView",
    params: {
      state: city.state,
      city: city.city,
    },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
};
</script>

<template>
  <div v-for="city in savedCities" :key="city.id" class="mt-3">
    <city-card :city="city" @click="goToCityView(city)" />
  </div>

  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>
