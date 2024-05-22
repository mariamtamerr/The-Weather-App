<script setup>
import { ref } from "vue";
import Modal from "./Modal.vue";
import { uid } from "uid";
import { useRoute, useRouter } from "vue-router";

const showModal = ref(false);

const toggleShowModal = () => {
  // this toggle would've been easier, but I'm practicing on emit from modal child to parent navbar
  //   showModal.value = !showModal.value;
  showModal.value = true;
};

const closeModal = () => {
  showModal.value = false;
};

// /// add a new city to favorites //////////////

const route = useRoute();
const router = useRouter();

const savedCities = ref([]);

const addCity = () => {
  // if there are saved cities in local storage then show them (json.parse them to be read 3shan homa byb2o objects t2rebn msh string)
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    console.log(savedCities.value);

    // create the item obj asln 3shan n3rf n3mlha add
    const cityObject = {
      id: uid(),
      state: route.params.state,
      city: route.params.city,
      coords: {
        lat: route.query.lat,
        lng: route.query.lng,
      },
    };

    // push the city to the array of saved cities
    savedCities.value.push(cityObject);

    // refresh local storage to make sure new city is added there
    // set item takes ('key', value)
    localStorage.setItem("savedCities", JSON.stringify(savedCities.value));
  }

  // if city is added, then delete preview=true from route query
  // Creating a Shallow Copy: Object.assign({}, route.query)
  // creates a new object and copies all enumerable properties from route.query to this new object.
  // The syntax is : Object.assign(target, ...sources)
  let query = Object.assign({}, route.query); // here the target is a new empty object
  delete query.preview;
  // query.id = cityObject.id;
  router.replace({ query });
};
</script>

<template>
  <header class="sticky container top-0 bg-weather-primary shadow-xl">
    <div class="parent py-6 flex justify-between">
      <router-link :to="{ name: 'home' }">
        <div class="left flex flex-col sm:flex-row gap-2">
          <i class="fa-solid fa-sun text-white text-2xl"></i>
          <h3 class="text-white text-2xl">The Local Weather</h3>
        </div>
      </router-link>

      <div class="right flex gap-3">
        <i
          @click="toggleShowModal"
          class="fa-solid fa-circle-info text-white text-xl hover:text-weather-seocondary duration-150 cursor-pointer"
        ></i>
        <i
          class="fa-solid fa-plus text-white text-xl cursor-pointer"
          @click="addCity"
          v-if="route.query.preview === 'true'"
        ></i>
      </div>
    </div>
  </header>

  <!-- modal -->

  <modal v-show="showModal" @close="closeModal"></modal>

  <!-- end modal -->
</template>
