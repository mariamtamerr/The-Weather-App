<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "@/components/CityCardSkeleton.vue";

const searchQuery = ref("");
const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const searchResults = ref(null);
const searchErrors = ref(null);
// to wait for user a few seconds after each each stroke to avoid making mulitple requests for each key stroke he writes
const queryTimeout = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);

  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value.length > 0) {
      try {
        const results = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );

        searchResults.value = results.data.features;
        console.log(searchResults.value);
      } catch (e) {
        searchErrors.value = true;
        console.log(e);
      }

      return;
    }

    // after finishing try catch rg3 kool haga null
    searchResults.value = null;
  }, 300);
};

// preview city
const router = useRouter();

const previewCity = (searchResult) => {

  const [city, state] = searchResult.place_name.split(',');
  console.log(city, state) // ohio Illinois
  router.push({
    name:'CityView',
    params: { state: state.replaceAll(" ", ""), city: city},
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true, 
    }
  })
  // console.log(searchResult);
};
</script>

<template>
  <main>
    <div
      class="search container  text-white p-3 rounded max-w-screen-md bg-weather-seocondary"
    >
      <!-- @input="" means live searching , aka calling function getSearchResults whenever something in input is being typed  -->
      <input
        v-model="searchQuery"
        @input="getSearchResults"
        class="bg-transparent pb-3 border-b w-full h-full container outline-none"
        type="search"
        placeholder="Search for a city or state, ex: 'Ohio'"
      />

      <div class="searchResults container bg-weather-seocondary w-full">
        <h4 v-if="searchErrors">
          Sorry, something went wrong, please try again.
        </h4>
        <!-- <h4 class="py-2" v-if="!searchErrors && searchResults.length === 0"> -->
        <h4
          class="py-2"
          v-if="!searchErrors && searchResults && searchResults.length === 0"
        >
          No results match your query, try a different term
        </h4>
        <div v-else>
          <div
            v-for="searchResult in searchResults"
            :key="searchResult.id"
            class="searchResult"
          >
            <h4
              class="mt-2 cursor-pointer hover:bg-weather-primary hover:p-2 mb-2"
              @click="previewCity(searchResult)"
            >
              {{ searchResult.place_name }}
            </h4>
          </div>
        </div>
      </div>

     
    </div>

    <div class="container mt-10 mb-16 text-white p-3 rounded max-w-screen-md ">
        <Suspense>
          <city-list />
          <!-- <CityList/> -->
          <template #fallback>
            <!-- <p>loading..</p> -->
            <CityCardSkeleton/>
          </template>
        </Suspense>
      </div>

  </main>
</template>
