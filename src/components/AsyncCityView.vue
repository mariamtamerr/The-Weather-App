<script setup>
import axios from "axios";
import { ref, computed, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";

const route = useRoute(); // from current route /weather/:state/:city
const router = useRouter();
const savedCities = ref([]);


// ------- make remove city only appear if it's saved  ------


// Load saved cities from local storage on component mount
onMounted(() => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }
});

// Check if the current city is saved
const isCitySaved = computed(() => {
  return savedCities.value.some(
    (city) =>
      city.state === route.params.state &&
      city.city === route.params.city
  );
});






// ---------------------



const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
    );

    // calc current date & time depending on ur timezone
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });

    //   Flicker Delay for skeleton or better UI when loading
    await new Promise((res) => setTimeout(res, 1000));

    return weatherData.data;
  } catch (error) {
    console.error(error);
  }
};

const weatherData = await getWeatherData();

// remove city using unique id ////////////////
  // easiest way is to add id of city to router query string
  // const router = useRouter();

  const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem("savedCities"));
    // filter the array of cities and delete the city with this id 
    const updatedCities = cities.filter((city) => city.id !== route.query.id); 

    // then refresh/update the local storage
    localStorage.setItem("savedCities", JSON.stringify(updatedCities) );

    // after delete, redirect to home page
    router.push({
      name: "home",
    })
  };

</script>

<template>
  <div>
    <!-- banner -->
    <div
      v-if="route.query.preview"
      class="mx-auto text-center p-4 bg-weather-seocondary text-white"
    >
      <p>
        You are currently previewing this city, click the "+" icon to start
        tracking this city.
      </p>
    </div>

    <!-- weather overview -->
    <div class="text-center text-white py-12">
      <h1 class="cityName text-4xl mb-2">{{ route.params.city }}</h1>
      <h1 class="date&time mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString("en-us", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}

        {{
          new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
            timeStyle: "short",
          })
        }}
      </h1>

      <h1 class="temp text-7xl mb-5">
        {{ Math.round(weatherData.current.temp) }}&deg;
      </h1>
      <p class="feelsLike">
        Feels like
        {{ Math.round(weatherData.current.feels_like) }} &deg;
      </p>
      <p class="description">
        {{ weatherData.current.weather[0].description }}
      </p>
      <img
        class="w-[150px] h-auto mx-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
        alt=""
      />
    </div>

    <hr class="border-white border border-opacity-10 -mt-7" />

    <!-- hourly weather -->
    <div class="container text-white py-12">
      <h2 class="mb-5 font-bold">Hourly Weather</h2>
      <div class="flex gap-10 overflow-x-scroll scrollbar-hide">
        <div
          v-for="hourData in weatherData.hourly"
          :key="hourData.dt"
          class="whitespace-nowrap"
        >
          <p>
            {{
              new Date(hourData.currentTime).toLocaleTimeString("en-us", {
                hour: "numeric",
              })
            }}
          </p>
          <img
            class="w-auto h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
            alt=""
          />
          <p class="text-xl">{{ Math.round(hourData.temp) }}&deg;</p>
        </div>
      </div>
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Weekly Weather -->
    <div class="container pt-12 pb-28">
      <div class="mx-8 text-white">
        <h2 class="mb-4 font-bold">7 Day Forecast</h2>
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="flex items-center"
        >
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("en-us", {
                weekday: "long",
              })
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>H: {{ Math.round(day.temp.max) }}</p>
            <p>L: {{ Math.round(day.temp.min) }}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- removing a city -->
    <div
      v-if="isCitySaved"
      @click="removeCity"
      class="flex justify-center text-lg items-center -mt-10 gap-2 cursor-pointer text-white hover:text-red-500 duration-150"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>
