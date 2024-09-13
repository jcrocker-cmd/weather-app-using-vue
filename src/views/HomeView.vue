<template>
  <main class="container text-white">
    <div clas="pt-4 mb-8 relative">
        <input v-model="searchQuery"
        @input="getSearchResults"
        type="text" name="" id="" placeholder="Search for city or state"
        class="py-2 px-1 w-full border-b focus:border-weather-secondary focus:outline-none
        focus:shadow-[0px_1px_0_9_#004E71] bg-transparent">

        <ul class="absolute bg-weather-secondary w-full text-white shadow-md py-2 px-1"
        v-if="mapboxSearchResults"
        >
          <p v-if="searchError">Sorry, Something went wrong</p>

          <p v-if="!serverError && mapboxSearchResults.length === 0">No results try different term</p>

          <template v-else> 
            <li v-for="searchResult in mapboxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
            >
            

            {{searchResult.place_name}}
            </li>
          </template>
        </ul>
    </div>

    <div class="flex flex-col gap-4">
      <Suspense>
          <CityList />
          <template #fallback>
            <!-- <p>Loading...</p> -->
          <CityCardSkeleton />
          </template>
      </Suspense>
    </div>

  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import {useRouter} from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";

const router = useRouter();
const previewCity= (searchResult) => {
  console.log(searchResult);
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: 'cityView',
    params: {state: state.replaceAll(" ",""), city: city},
    query:{
      lat:searchResult.geometry.coordinates[1],
      lng:searchResult.geometry.coordinates[0],
      preview: true,
    }
  })
};



//MapBox API
const mapboxAPIKey ="pk.eyJ1Ijoiam9obm5vdGUiLCJhIjoiY20wbmsybGJrMGVxNzJscHZxamZ4cWU3diJ9.6qq6aC3O8WswGkN3NY6nMg"
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async() => {
    if (searchQuery.value  !== "") {
      try {
        const result = await axios.get(
        // `https://api.mapbox.com/search/geocode/v6/forward?q=${searchQuery.value}.json?access_token=${mapboxAPIKey}`
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${encodeURIComponent(searchQuery.value)}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
        console.log(mapboxSearchResults.value);
      } catch {
        searchError.value = true;
      }

      return;
    };
    mapboxSearchResults.value = null;

  }, 300);
};

</script>

<style scoped>

.container
{
  width: 100%;
}
.container ul
{
  width: 35%;
}
</style>