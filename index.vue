<script setup>
import { ref } from 'vue';
import AddLocation from '~/components/AddLocation.vue';
import ForecastSidebar from '~/components/ForecastSidebar.vue';

const showAddModal = ref(false);
const selectedLocation = ref(null);

const openSidebar = (location) => {
  selectedLocation.value = location;
};
</script>

<template>
  <div>
    <button @click="showAddModal = true" class="bg-green-500 text-white px-4 py-2">+ Add Location</button>
    <table>
      <tr v-for="loc in locations" :key="loc.id" @click="openSidebar(loc)">
        <td>{{ loc.name }}</td>
      </tr>
    </table>

    <AddLocation v-if="showAddModal" @locationAdded="showAddModal = false" />
    <ForecastSidebar :selectedLocation="selectedLocation" />
  </div>
</template>
