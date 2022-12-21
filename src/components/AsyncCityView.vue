<template>
    <div class="flex flex-col flex-1 items-center bg-weather-primary">
            <p v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
                You are currently previewing {{ route.params.city }}, {{ route.params.country }}. If you want to track this city, click on the "+" icon.
            </p>
            <div class="flex flex-col items-center text-white py-12">

                <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
                <p class="text-sm mb-12">
                    {{ 
                        new Date(weatherData.currentTime).toLocaleDateString(
                            "en", {
                                weekday: "long",
                                day: "2-digit",
                                month: "long"
                            }
                        )
                     }}, 
                     {{ 

                        new Intl.DateTimeFormat('en', { timeStyle: "short" }).format(new Date(weatherData.currentTime))    

                      }}.
                </p>
                <p class="text-8xl mb-8">
                    {{ Math.round(weatherData.current.temp) }}&deg;C
                </p>
                <div class="text-center">
                    <p>{{ weatherData.current.weather[0].description.charAt(0).toUpperCase() + weatherData.current.weather[0].description.slice(1) }}.</p>
                    <p>Wind: {{ Math.round(weatherData.current.wind_speed * 3.6) }} km/h</p>
                    <p>Humidity: {{ weatherData.current.humidity }}%</p>
                </div>
                <img class="w-[150px] h-auto" :src="
                    `http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`
                " >
            </div>

            <hr class="border-white border-opacity-10 border w-full">

            <div class="max-w-screen-md w-full py-12 mx-auto">
                <div class="mx-8 text-white">
                    <h2 class="mb-4">Hourly Weather</h2>
                    <div class="flex gap-10 overflow-x-scroll scrollbar" >
                        <div v-for="hourWeather in weatherData.hourly" :key="hourWeather.dt"
                        class="flex flex-col gap-4 items-center">

                            <p class="whitespace-nowrap text-md">
                                {{ 
                                    new Intl.DateTimeFormat('en', { timeStyle: "short" }).format(new Date(hourWeather.currentTime))    
                                 }}
                            </p>
                            <img :src="`http://openweathermap.org/img/wn/${hourWeather.weather[0].icon}@2x.png`" class="w-[45px] h-auto">
                            <p class="text-xl">
                                {{ Math.round(hourWeather.temp) }}&deg;C
                            </p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="max-w-screen-md w-full py-12 mx-auto">
                <div class="mx-8 text-white">
                    <h2 class="mb-4">Daily Weather</h2>
                    <div class="flex flex-col gap-10">
                        <div v-for="dayWeather in weatherData.daily" :key="dayWeather.dt"
                        class="flex gap-4 items-center justify-around">

                            <p class="whitespace-nowrap text-md">
                                {{ new Date(dayWeather.dt * 1000).toLocaleDateString("en", { weekday: "long"}) }}
                            </p>
                            <div class="flex items-center">
                                <img :src="`http://openweathermap.org/img/wn/${dayWeather.weather[0].icon}@2x.png`" class="w-[45px] h-auto">
                                <p class="text-xl">
                                    {{ Math.round(dayWeather.temp.day) }}&deg;C
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
            @click="removeCity">
                <i class="fa-solid fa-trash"></i>
                <p>Remove city</p>
            </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();

const APIkey = '59a39d51f9f8857e1587bd38daead9e3';

const  getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lon}&appid=${APIkey}&units=metric`)


        // cal current date & time
        const localOffset = new Date().getTimezoneOffset() * 60000;
        const utc = weatherData.data.current.dt * 1000 + localOffset;
        weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;
        // cal hourly weather offset
        weatherData.data.hourly.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
        });

        await new Promise((res) => setTimeout(res, 1000))

        return weatherData.data;
    } catch(err) {
        console.log(err);
    }
}

const router = useRouter();

const removeCity = () => {

    const cities = JSON.parse(localStorage.getItem('savedCities'));

    localStorage.setItem('savedCities', JSON.stringify(cities.filter(city => city.id !== route.query.id)))
    
    router.push({ name: 'home' });
}

const weatherData = await getWeatherData();

</script>

<style>

    .scrollbar::-webkit-scrollbar {
        height: 1px;
    }
</style>