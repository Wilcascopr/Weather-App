<template>
    <div v-if="savedCities.length">
        <div v-for="city in savedCities" :key="city.id">
            <CityItem :city="city" @click="redirect(city)"/>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import CityItem from './CityItem.vue';
import { useRouter } from 'vue-router';

const APIkey = '59a39d51f9f8857e1587bd38daead9e3';
const savedCities = ref([]);
const router = useRouter();

const getCities = async () => {
    if(localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));

        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lon}&appid=${APIkey}&units=metric`)
            )
        });

        const weatherData = await Promise.all(requests);

        await new Promise((res) => setTimeout(res, 1000))

        weatherData.forEach((weatherCity, index) => {
            savedCities.value[index].weather = weatherCity.data;
        })
    }

} 

const redirect = (city) => {
    router.push({ name: 'cityView', 
        params: { country: city.country, city: city.city },
        query: {
            lat: city.coords.lat,
            lon: city.coords.lon,
            id: city.id
        }
        })
}

await getCities();
</script>

<style>

</style>