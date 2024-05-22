<script setup>
import { RouterView } from "vue-router";
import Navbar from "./components/Navbar.vue";

components: {
  Navbar;
}
</script>

<template>
  <div>
    <div class="bg-weather-primary min-h-screen pb-10">
      <navbar />
      <RouterView v-slot="{ Component }">
        <Transition name="page" mode="out-in">
          <component :is="Component" />
        </Transition>
      </RouterView>
    </div>
  </div>
</template>

<style>
.page-enter-active,
.page-leave-active {
  transition: 600ms ease all;
}

.page-enter-from,
.page-leave-to {
  opacity: 0;
}
</style>
