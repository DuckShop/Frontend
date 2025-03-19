<script setup>
import { ref, watch } from "vue";

const props = defineProps(["selectedLocation"]);
const forecast = ref([]);
const emit = defineEmits();

watch(
  () => props.selectedLocation,
  async (selectedLocation) => {
    if (selectedLocation) {
      const response = await fetch(
        `http://127.0.0.1:8000/forecast/${selectedLocation}`
      );
      forecast.value = await response.json();
      console.log(forecast.value);
    }
  }
);

const hideModal = () => {
  emit("close"); // Emit the 'close' event to notify the parent to hide the modal
};

const formatDateToWeekday = (dateString) => {
  const date = new Date(dateString);
  return date.toLocaleString("en-US", { weekday: "long" }); // Get the day of the week (e.g., "Wednesday")
};

const getWeatherIcon = (condition) => {
  switch (condition) {
    case 4: // Overcast
      return new URL("~/assets/icons/4.svg", import.meta.url).href;
    case 9: // Thunderstorm
      return new URL("~/assets/icons/2.svg", import.meta.url).href;
    case 3: // Mostly cloudy
      return new URL("~/assets/icons/3.svg", import.meta.url).href;
    case 6: // Light rain
      return new URL("~/assets/icons/5.svg", import.meta.url).href;
    default: // Default icon for other conditions
      return new URL("~/assets/icons/0.svg", import.meta.url).href; // Clear sky or any other condition
  }
};
</script>

<template>
  <div
    v-if="selectedLocation"
    class="fixed inset-0 bg-gray-800 bg-opacity-50 flex items-center justify-center"
    @click="hideModal"
  ></div>
  <div
    v-if="selectedLocation"
    class="fixed right-0 top-0 h-full w-[450px] bg-[#1B1B1D] shadow-lg p-8"
  >
    <div
      class="text-2xl absolute right-8 bg-[#252528] text-white text-center mt-[-5px] justify-center items-center flex w-[30px] h-[30px] pb-[6px] rounded-md cursor-pointer hover:bg-[#424247]"
      @click="hideModal"
    >
      ×
    </div>
    <h2 class="text-4xl font-[760]">{{ selectedLocation }}</h2>
    <h3 class="text-xl mt-6 text-[#AAAAAA]">This Week</h3>
    <ul>
      <li
        class="bg-[#323232] mb-2 p-6 justify-between gap-x-4 rounded-2xl mt-4 flex flex-row"
        v-for="day in forecast.forecast"
        :key="day.date"
      >
        <div class="flex flex-row items-center gap-x-3">
          <img :src="getWeatherIcon(day.weather_condition)" class="w-12 h-12" />
          <div class="text-3xl">{{ formatDateToWeekday(day.date) }}</div>
        </div>
        <div class="text-md w-[100px] flex flex-col">
          <div class="flex flex-row justify-between">
            <span class="text-left text-[#BEBEBE]">Min.</span
            ><span class="text-right font-bold"
              >{{ day.min_temperature }} °C</span
            >
          </div>
          <div class="flex flex-row justify-between">
            <span class="text-left text-[#BEBEBE]">Max.</span
            ><span class="text-right font-bold"
              >{{ day.max_temperature }} °C</span
            >
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>
