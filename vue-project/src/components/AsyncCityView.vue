<template>
    <div></div>
  </template>
  
  <script setup>
  import axios from "axios";
  import { useRoute } from "vue-router";
  
  const route = useRoute();
  const getWeatherData = async () => {
    try {
      const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=90fa0d1ccb679b2b539ba3792efe08df&units=imperial`
      );
  
      // cal current date & time to handle time zone difference
      const localOffset = new Date().getTimezoneOffset() * 60000;
      //convert to utc format
      const utc = weatherData.data.current.dt * 1000 + localOffset;
      weatherData.data.currentTime =
        utc + 1000 * weatherData.data.timezone_offset;
  
      // cal hourly weather offset to handle time zone difference
      weatherData.data.hourly.forEach((hour) => {
        const utc = hour.dt * 1000 + localOffset;
        hour.currentTime =
          utc + 1000 * weatherData.data.timezone_offset;
      });
  
      return weatherData.data;
    } catch (err) {
      console.log(err);
    }
  };
  const weatherData = await getWeatherData();
  </script>