<template>
  <div class="container text-white ">
      <div class="pt-4 mb-8 relative">
          <input type="text" placeholder="Search for a city"
          v-model="searchQuery" @input="getSearchResults"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary
          focus:outline-none foucs:shadow-[0px_1px_0_0_#004E71]"
          />
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
      v-if="mapboxSearchResults">
          <p v-if="searchError">
            Sorry something went wrong please try again.
          </p>
          <p v-if="!searchError && !mapboxSearchResults.length">
            No resulst match your query, try a different term.
          </p>
          <template v-else>
            <li v-for="place in mapboxSearchResults" :key="place.id"
            class="py-2 cursor-pointer"
            @click="previewCity(place)">
                {{  place.place_name  }}
            </li>
          </template>
      </ul>
      </div>
      <div class="flex flex-col gap-4">
          <Suspense>
              <CityList/>
              <template #fallback>
                <CityCardSkeleton/>
              </template>
          </Suspense>
      </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';
import CityList from '@/components/CityList.vue';
import CityCardSkeleton from '@/components/CityCardSkeleton.vue';

const searchQuery = ref('');
const queryTimeOut = ref(null);
const mapboxAPI = "pk.eyJ1Ijoid2lsY2FzY29wciIsImEiOiJjbGJ2NGZvZ3kwazhvM29wZ3I5azVxbG0yIn0.LYLJaLiKvgLgRTwIax1bvw";
const mapboxSearchResults = ref(null);
const searchError = ref(null);
const router = useRouter();

const previewCity = (searchResult) =>{
  const arrResults = searchResult.place_name.split(',');
  let city, country;
  if (arrResults.length === 3) { 
    city = arrResults[0];
    country = arrResults[2];
  } else { 
    city = arrResults[0];
    country = arrResults[1]; 
  }

  router.push({ name: 'cityView', 
  params: { country: country.trim(), city: city.trim() },
  query: {
    lat: searchResult.geometry.coordinates[1],
    lon: searchResult.geometry.coordinates[0],
    preview: true
  }
  })
    
}

const getSearchResults = () => {
    clearTimeout(queryTimeOut.value)
    queryTimeOut.value = setTimeout(async () => {
      if (searchQuery.value !== '') {
        try {
          const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPI}&types=place`);
          mapboxSearchResults.value = result.data.features;
        } catch(err) {
          console.log(err)
        }
        return
      }
      mapboxSearchResults.value = null;
    }, 300)
}
</script>
