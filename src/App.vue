<template>
    <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
        <NavigationComp />
        <RouterView />
    </div>
</template>

<script setup>
import { RouterView } from 'vue-router';
import NavigationComp from './components/NavigationComp.vue';

</script>

<style>

</style>
