<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"> 
            <RouterLink :to="{ name: 'home' }">
                <div class="flex items-center gap-3">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">The local weather</p>
                </div>
            </RouterLink>


            <div class="flex gap-3 flex-1 justify-end ">
                <i class="fa-solid fa-circle-info text-xl 
                hover:text-weather-secondary duration-500 cursor-pointer"
                @click="(modal = !modal)"></i>
                <i class="fa-solid fa-plus text-xl
                hover:text-weather-secondary duration-500 cursor-pointer"
                @click="addCity" v-if="route.query.preview"></i>
            </div>
            <BaseModal v-if="modal" @closeModal="(modal = !modal)">
                <div class="text-black">
                    <p class="mb-4">
                        The local weather allows you to track the current and future weather of cities of yout choice.
                    </p>
                    <h2 class="text-2xl">How it works: </h2>
                    <ol class="list-decimal list-inside mb-4">
                        <li>
                            Search for yout city by entering the name into the search bar.
                        </li>
                        <li>
                            Select a city within the results, this will take you to the current weather for your selection.
                        </li>
                        <li>
                            Track the city by clicking on the '+' icon in the top right. This will save the city to view a later time from the home page.
                        </li>
                    </ol>
                    <h2 class="text-2xl">Removing a city</h2>
                    <p>
                        If you no longer wish to track a city, simply select
                        the city within the home page. At the bottom of the
                        page, there will be am option to delete the city.
                    </p>
                </div>
            </BaseModal>
        </nav>
    </header>
</template>

<script setup>
import { ref } from 'vue';
import { RouterLink, useRoute, useRouter } from 'vue-router';
import BaseModal from './BaseModal.vue';

const modal = ref(false)
const savedCities = ref([])
const route = useRoute();
const router = useRouter();

const uID = () => {
    const chars = 'qwert8yuiop3lkjh90gfd76sazxc54vbn12m'
    let id = '';
    for (let i = 0; i < 15; i++) {
        let num =  Math.round(Math.random()*35);
        id += chars[num];
    }
    return id;
}

const addCity = () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    }

    const locationObj = {
        id: uID(),
        country: route.params.country,
        city: route.params.city,
        coords: {
            lat: route.query.lat,
            lon: route.query.lon
        }
    }

    savedCities.value.push(locationObj);

    localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

    let query = Object.assign({}, route.query);
    delete query.preview;
    query.id = locationObj.id
    router.replace({ query })
}
</script>

