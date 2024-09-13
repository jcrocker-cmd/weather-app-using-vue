<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigation />
    <RouterView v-slot="{ Component }">
      <Transition name="page" mode="out-in">
        <component :is="Component" />
      </Transition>
    </RouterView>
  </div>
</template>

<script setup>
  import { RouterView} from "vue-router";
  import SiteNavigation from "./components/SiteNavigation.vue";
</script>

<style>
.page-enter-active, 
.page-leave-active {
  transition: opacity 600ms ease-in-out;
}

.page-enter-from {
  opacity: 0;
}

.page-enter-to {
  opacity: 1;
}

.page-leave-from {
  opacity: 1;
}

.page-leave-to {
  opacity: 0;
}
</style>
