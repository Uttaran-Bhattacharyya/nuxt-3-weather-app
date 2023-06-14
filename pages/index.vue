<script setup lang="ts">
useHead({
  title: 'Weather App',
});

const city = ref('');
const geoData = ref<any>([]);
const weatherData = ref<any>();
const lat = ref('');
const lon = ref('');

const weather = computed(() => {
  return weatherData.value.weather[0].main;
});

const temp = computed(() => {
  return Math.round(weatherData.value.main.temp - 273.15);
});

const tempMin = computed(() => {
  return Math.round(weatherData.value.main.temp_min - 273.15);
});

const tempMax = computed(() => {
  return Math.round(weatherData.value.main.temp_max - 273.15);
});

const pressure = computed(() => {
  return weatherData.value.main.pressure;
});

const windSpeed = computed(() => {
  return weatherData.value.wind.speed;
});

const clouds = computed(() => {
  return weatherData.value.clouds.all;
});

async function onSubmit() {
  const { pending, data } = await useFetch<any>(
    `http://api.openweathermap.org/geo/1.0/direct?q=${city.value}&limit=1&appid=f845ac34e5db4828607d1c9d4e1ada8c`,
  );

  geoData.value = data.value;

  lat.value = data.value[0].lat;
  lon.value = data.value[0].lon;
}

async function onClick() {
  const { pending, data } = await useFetch<any>(
    `https://api.openweathermap.org/data/2.5/weather?lat=${lat.value}&lon=${lon.value}&appid=f845ac34e5db4828607d1c9d4e1ada8c`,
  );

  weatherData.value = data.value;
}
</script>

<template>
  <div class="mx-auto max-w-7xl px-4 py-4">
    <h1 class="text-4xl text-[#e96e4f]">Weather in your city</h1>
  </div>
  <div class="flex h-32 flex-row items-center justify-center bg-[#e96e4f]">
    <div class="mx-auto max-w-7xl">
      <form @submit.prevent="onSubmit">
        <input v-model="city" type="search" class="py rounded-xl px-2 focus:border-none" />

        <button type-="submit" class="inline-block rotate-45 cursor-pointer px-2 text-2xl text-[#3a3a3c]">
          &#9906;
        </button>
      </form>
    </div>
  </div>
  <div class="mx-auto max-w-7xl px-4 py-4 text-center">
    <div class="mx-auto grid w-full grid-cols-2 text-left md:w-4/5" v-if="geoData[0]">
      <p>Your city</p>
      <p>{{ geoData[0].name }}, {{ geoData[0].state }}</p>
      <p>Latitude</p>
      <p>{{ geoData[0].lat }}</p>
      <p>Longitude</p>
      <p>{{ geoData[0].lon }}</p>
    </div>
    <button v-if="geoData[0]" @click="onClick" class="mt-10 rounded-full bg-[#e96e4f] px-3 py-2 text-white">
      Check weather
    </button>
  </div>
  <div class="mx-auto max-w-7xl px-4 py-4">
    <div class="mx-auto w-full md:w-4/5" v-if="weatherData">
      <p class="w-fit rounded-full bg-[#3a3c3c] px-2 text-center text-white">{{ temp }}&deg;C</p>
      <p class="mt-2">
        Weather: {{ weather }}, temperature from {{ tempMin }} to {{ tempMax }}&deg;C, wind {{ windSpeed }} m/s, clouds
        {{ clouds }}%, {{ pressure }} hpa
      </p>
    </div>
  </div>
</template>

<style scoped></style>
