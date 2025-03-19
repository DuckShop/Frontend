<script setup>
import SearchIcon from "@mui/icons-material/Search";
import { ref } from "vue";

const newLocation = ref("");
const emit = defineEmits(["locationAdded"]);

const addLocation = async () => {
  if (!newLocation.value) return alert("Select a location!");

  await fetch("http://127.0.0.1:8000/locations", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name: newLocation.value }),
  });

  emit("locationAdded");
};

const clearinput = () => {
  newLocation.value = "";
};

const hideModal = () => {
  emit("locationAdded");
};
</script>

<template>
  <div
    class="fixed inset-0 bg-gray-800 bg-opacity-50 flex items-center justify-center"
    @click="hideModal"
  ></div>
  <div
    class="fixed bg-[#1B1B1D] text-white w-[350px] h-[200px] top-[calc(50%-100px)] left-[calc(50%-175px)] p-6 rounded shadow-lg"
  >
    <div class="flex flex-row justify-between items-center">
      <h2 class="text-2xl font-bold mb-4">Add Location</h2>
      <div
        class="text-2xl bg-[#252528] text-white text-center mt-[-5px] justify-center items-center flex w-[30px] h-[30px] pb-[6px] rounded-md cursor-pointer hover:bg-[#424247]"
        @click="hideModal"
      >
        ×
      </div>
    </div>
    <div
      class="bg-[#313030] flex flex-row justify-between rounded-md p-2 w-full"
    >
      <input
        v-model="newLocation"
        class="bg-none border-none bg-[#313030] outline-none"
        placeholder="Enter city name"
      />
      <div
        class="text-2xl text-white text-center mt-[0px] justify-center items-center flex w-[30px] h-[30px] pb-[6px] rounded-md cursor-pointer hover:bg-[#424247]"
        @click="clearinput"
      >
        ×
      </div>
    </div>
    <div class="flex justify-end cl mt-4">
      <button
        @click="addLocation"
        class="bg-[#D4E8F8] rounded-lg text-black px-8 py-2 w-full text-[16px] font-bold"
      >
        Add Location
      </button>
    </div>
  </div>
</template>
