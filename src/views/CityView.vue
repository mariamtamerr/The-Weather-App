<script setup>
import AsyncCityView from "../components/AsyncCityView.vue";
import CityViewSkeleton from "../components/CityViewSkeleton.vue";
</script>

<template>
  <div>
    <Suspense>
        <async-city-view />
        <template #fallback>
            <!-- <p>Loading .. </p> -->
            <CityViewSkeleton/>
        </template>
    </Suspense>
  </div>
</template>
