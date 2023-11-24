<template>
  <main>
    <div
      class="flex items-center justify-center py-6 px-5 bg-weather-secondary rounded-sm shadow-md cursor-pointer hover:bg-weather-primary transition duration-150 ease-linear border-weather-primary hover:border-weather-secondary hover:border-b">
      <div class="flex flex-col flex-1">
        <h2 class="text-3xl">{{ city.city }}</h2>
        <h3>{{ city.state }}</h3>
      </div>
      <!-- <div class="flex flex-1">
        <img
          class="w-[50px] h-auto"
          :src="`http://openweathermap.org/img/wn/${city.weather.weather[0].icon}@2x.png`"
          alt="" />
      </div> -->
      <div class="flex flex-col gap-2 justify-center items-center">
        <p class="text-3xl">
          {{ Math.round((city.weather.main.temp - 32) * (5 / 9)) }}
          <span class="text-sm absolute ml-0"> &#8451;</span>
        </p>
        <div class="flex gap-6">
          <p class="text-sm">
            H:
            {{ Math.round((city.weather.main.temp_max - 32) * (5 / 9)) }}
            <span class="text-sm absolute ml-0"> &#8451;</span>
          </p>
          <p class="text-sm">
            L:
            {{ Math.round((city.weather.main.temp_min - 32) * (5 / 9)) }}
            <span class="text-sm absolute ml-0"> &#8451;</span>
          </p>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
  defineProps({
    city: {
      type: Object,
      default: () => {},
    },
  });
</script>
