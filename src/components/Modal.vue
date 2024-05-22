<script setup>
// defineProps({
//   toggleShowModal: {
//     type: Boolean,
//     default: false,
//   },
// });
defineEmits(["close"]);
</script>

<template>
  <transition name="modal">
    <div class="modal-wrapper" @click.self="$emit('close')">
      <div
        class="modal bg-white mt-16 mb-10 container sm:max-w-md w-96 max-h-screen  rounded-lg"
      >
        <div class="modalContent p-5 ">
          <h3 class="font-bold">About:</h3>
          <p>
            The Local Weather allows you to track the current and
            future weather of cities of your choosing.
          </p>
          <h3 class="font-bold mt-3">How it works:</h3>
          <ol class="list-decimal ms- list-inside mb-4">
            <li>
              Search for your city by entering the name into the
              search bar.
            </li>
            <li class="mt-1">
              Select a city within the results, this will take
              you to the current weather for your selection.
            </li>
            <li class="mt-1">
              Track the city by clicking on the "+" icon in the
              top right. This will save the city to view at a
              later time on the home page.
            </li>
          </ol>
          <h3 class="font-bold">Removing a city:</h3>
          <p>
            If you no longer wish to track a city, simply select
            the city within the home page. At the bottom of the
            page, there will be am option to delete the city.
          </p>
          <button
            @click="$emit('close')"
            class="bg-weather-primary p-2.5 px-5 text-white rounded-xl mt-2 hover:bg-weather-seocondary"
          >
            Close
          </button>
        </div>
      </div>
    </div>
  </transition>
</template>

<style scoped>
/* modal inner */
.modal-enter-active {
  transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.1s;
}

.modal-leave-active {
  transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}

.modal-enter-from {
  opacity: 0;
  /* transform: scale(0.8); */
}

/* .modal-enter-to {
  transform: scale(0.8);
} */
</style>
