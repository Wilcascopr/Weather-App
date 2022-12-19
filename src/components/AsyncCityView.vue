<template>
    <div>

    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute } from 'vue-router';

const route = useRoute();

const APIkey = '1ffca2e994a485148fabf9d73d1ed878';

const  getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lon}&appid=${APIkey}&units=metric`);


        const localOffSet  = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffSet;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    } catch(err) {
        console.log(err);
    }
}

</script>
