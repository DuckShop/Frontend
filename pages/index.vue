<script setup>
import { ref, onMounted } from "vue";
import AddLocation from "~/components/AddLocation.vue";
import ForecastSidebar from "~/components/ForecastSidebar.vue";

const showAddModal = ref(false);
const selectedLocation = ref(null);
const loading = ref(true);
const locations = ref([]);

const openSidebar = (location) => {
  selectedLocation.value = location;
};

// Fetch locations from backend
const fetchLocations = async () => {
  loading.value = true;
  try {
    const response = await fetch("http://127.0.0.1:8000/locations");
    locations.value = await response.json();
  } catch (error) {
    console.error("Error fetching locations:", error);
  } finally {
    loading.value = false;
  }
};

const handleCloseSidebar = () => {
  selectedLocation.value = null;
};

onMounted(fetchLocations);

// Remove location based on name
const removeLocation = async (name, event) => {
  event.stopPropagation(); // Prevents the row click from triggering the sidebar
  if (confirm("Are you sure you want to remove this location?")) {
    await fetch(`http://127.0.0.1:8000/locations/${name}`, { method: "DELETE" });
    fetchLocations();
  }
};

const getWeatherIcon = (condition) => {
  switch (condition) {
    case 4:
      return new URL('~/assets/icons/4.svg', import.meta.url).href;
    case 9:
      return new URL('~/assets/icons/2.svg', import.meta.url).href;
    case 3:
      return new URL('~/assets/icons/3.svg', import.meta.url).href;
    case 6:
      return new URL('~/assets/icons/5.svg', import.meta.url).href;
    default:
      return new URL('~/assets/icons/0.svg', import.meta.url).href;
  }
};
</script>

<template>
  <div class="min-h-screen mx-auto container text-white p-6">
    <header class="flex items-center mb-6">
      <img src="~assets/icons/0.svg" alt="Weather Icon" class="w-8 h-8 mr-2" />
      <h1 class="text-2xl font-semibold">Weather App</h1>
    </header>

    <AddLocation v-if="showAddModal" @locationAdded="showAddModal = false" />
    <ForecastSidebar :selectedLocation="selectedLocation" @close="handleCloseSidebar" />
    <section class="py-6 rounded-lg shadow-lg">
      <div class="flex flex-row justify-between">
        <h2 class="text-3xl font-bold mb-4">Locations</h2>
        <button
          @click="showAddModal = true"
          class="bg-[#D4E8F8] rounded-lg text-black px-8 py-0 text-[16px] font-bold"
        >
          + Add Location
        </button>
      </div>

      <div v-if="loading" class="flex justify-center items-center text-white my-4">
        <div class="spinner-border text-white" role="status">
          <span class=" text-white text-xl">Loading...</span>
        </div>
      </div>

      <div class="overflow-x-auto pt-6">
        <table class="w-full border-collapse rounded-lg overflow-hidden">
          <thead>
            <tr class="bg-[#2C2C2C]">
              <th class="py-3 px-4 text-left w-[300px]">Location</th>
              <th class="py-3 px-4 text-center w-[200px]">Temperature</th>
              <th class="py-3 px-4 text-center w-[200px]">Rainfall</th>
              <th class="py-3 px-4 text-right"></th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="loc in locations"
              :key="loc.id"
              @click="openSidebar(loc.name)"
              class="border-b border-gray-700 bg-[#1B1B1D] hover:bg-[#272727] transition"
            >
              <td class="flex items-center gap-3 py-3 px-4">
                <img
                  :src="getWeatherIcon(loc.weather.weather_condition)"
                  class="w-6 h-6"
                />
                <span class="text-lg font-medium">{{ loc.name }}</span>
              </td>
              <td class="text-center py-3 px-4">
                {{ loc.weather.temperature }}°C
              </td>
              <td class="text-center py-3 px-4">{{ loc.weather.rain }} mm</td>
              <td class="text-right py-3 px-4">
                <!-- Add event.stopPropagation() here to prevent sidebar open -->
                <button
                  @click="removeLocation(loc.name, $event)"
                  class="bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded-lg transition"
                >
                  🗑️
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>
  </div>
</template>

