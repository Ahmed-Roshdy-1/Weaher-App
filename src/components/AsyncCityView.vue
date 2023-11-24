<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center">
      <p>You are currently previewing this city, click the '+' icon to start tracking this city</p>
    </div>

    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString("en-us", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
            timeStyle: "short",
          })
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round((weatherData.current.temp - 32) * (5 / 9)) }}
        <span class="text-4xl absolute"> &#8451;</span>
      </p>
      <p class="capitalize">{{ weatherData.current.weather[0].description }}</p>
      <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
        alt="" />
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Hourly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>
        <div
          id="hourlyTemp"
          class="flex gap-10 overflow-x-scroll">
          <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center">
            <p class="whitespace-nowrap text-md">
              {{
                new Date(hourData.currentTime).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt="" />
            <p class="text-xl relative">
              {{ Math.round((hourData.temp - 32) * (5 / 9)) }}
              <span class="text-sm absolute top-0"> &#8451;</span>
            </p>
          </div>
        </div>
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">7 Day Forecast</h2>
        <div
          class="flex items-center"
          v-for="day in weatherData.daily"
          :key="day.dt">
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("en-us", {
                weekday: "long",
              })
            }}
          </p>
          <img
            class="w-auto h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt="" />
          <div class="flex gap-8 flex-1 justify-end">
            <p>
              H:{{ Math.round((day.temp.max - 32) * (5 / 9)) }}
              <span class="text-sm absolute"> &#8451;</span>
            </p>
            <p>
              L:{{ Math.round((day.temp.min - 32) * (5 / 9)) }}
              <span class="text-sm absolute"> &#8451;</span>
            </p>
          </div>
        </div>
      </div>
    </div>

    <!-- Delete Button -->
    <div
      v-if="!route.query.preview"
      @click="removeCity"
      class="flex item-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500">
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
  // 39b4245e60fe4f5bed5034fa9c913457
  import axios from "axios";
  import { useRoute, useRouter } from "vue-router";
  import { ref } from "vue";

  const route = useRoute();
  async function getWeatherData() {
    try {
      const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
      );

      // cal current date & time
      const localOffset = new Date().getTimezoneOffset() * 60000;
      const utc = weatherData.data.current.dt * 1000 + localOffset;
      weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset;

      // cal hourly weather offset

      weatherData.data.hourly.forEach((hour) => {
        const utc = hour.dt * 1000 + localOffset;
        hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
      });

      //Flicker Delay
      await new Promise((res) => setTimeout(res, 500));

      return weatherData.data;
    } catch (err) {
      console.log(err);
    }
  }

  const weatherData = await getWeatherData();
  // console.log(weatherData);

  const router = useRouter();
  function removeCity() {
    const cities = JSON.parse(localStorage.getItem("savedCities"));

    const updatedCities = cities.filter((city) => city.id != route.query.id);
    console.log(updatedCities);

    localStorage.setItem("savedCities", JSON.stringify(updatedCities));

    router.push({
      name: "home",
    });
  }
</script>

<style scoped>
  #hourlyTemp::-webkit-scrollbar {
    width: 10px;
  }

  /* Track */
  #hourlyTemp::-webkit-scrollbar-track {
    background: #f4bf96;
    border-radius: 2rem;
  }

  /* Handle */
  #hourlyTemp::-webkit-scrollbar-thumb {
    background: #ce5a67;
    border-radius: 2rem;
  }

  /* Handle on hover */
  #hourlyTemp::-webkit-scrollbar-thumb:hover {
    background: #742b33;
  }
</style>
