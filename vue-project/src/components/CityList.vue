<template>
    <div v-for="city in savedCities" :key="city.id">
      <!-- One city per card -->
      <CityCard :city="city" @click="goToCityView(city)" />
    </div>
    <p v-if="savedCities.length === 0">
      No locations added. Search in
      the field above to add a city.</p>
  </template>
  
  <script setup>
  import axios from "axios";
  import { ref } from "vue";
  import { useRouter } from "vue-router";
  import CityCard from "./CityCard.vue";

  const savedCities = ref([]);
  const getCities = async () => {

    if (localStorage.getItem("savedCities")) {
      savedCities.value = JSON.parse(
        localStorage.getItem("savedCities"));

      //OpenWeather API Current Weather 
      const requests = [];
      savedCities.value.forEach((city) => {
        requests.push(
          axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
          )
        );
      });

      const weatherData = await Promise.all(requests);
      //Just delay a little bit to show the animation
      await new Promise((res) => setTimeout(res, 600));
      weatherData.forEach((value, index) => {
        savedCities.value[index].weather = value.data;
      });
    }
  };
  await getCities();
  const router = useRouter();
  const goToCityView = (city) => {
    router.push({
      name: "cityView",
      params: { state: city.state, city: city.city },
      query: {
        id: city.id,
        lat: city.coords.lat,
        lng: city.coords.lng,
      },});};
  </script>