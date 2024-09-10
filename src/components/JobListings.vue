<script setup>
import SingleJobList from "./SingleJobList.vue";

//import jobData from "@/data/jobs.json";

import { RouterLink } from "vue-router";
import { ref, defineProps, onMounted, reactive } from "vue";
import axios from "axios";
import PulseLoader from "vue-spinner/src/PulseLoader.vue";

defineProps({
  limit: { type: Number, default: 100 },
  showButton: { type: Boolean, default: false },
});

//const jobs = ref(jobData);

// store jobs in ref
const state = reactive({
  jobs: [],
  isLoading: true,
  error: null
});

// fetch data from json server
onMounted(async () => {
  try {
    const response = await axios.get("/api/jobs");
    state.jobs = response.data;

  } catch (error) {
    state.error = error.message;
    console.log(error);
  } finally {
    state.isLoading = false;
  }
});

</script>

<template>
  <!-- Browse Jobs -->
  <section class="bg-green-50 px-4 py-10 pb-36">
    <div class="container-xl lg:container m-auto">
      <h2 class="text-3xl font-bold text-green-500 mb-10 text-center">
        Browse Jobs
      </h2>

      <!-- Show loading spiner while loading is true -->
      <div v-if="state.isLoading" class="text-center text-gray-500 py-12">
        <PulseLoader />
      </div>

      <!-- Show error message if error is not null -->
      <div v-else-if="state.error" class="text-red-500 mb-6 text-center">
        <p class="text-red-700">No Jobs Found.</p>
        <p class="text-red-800">{{ state.error }}</p>
      </div>

      <!-- Show jobs if loading is false -->
      <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <SingleJobList v-for="job in state.jobs.slice(0, limit)" :key="job.id" :job="job" />
      </div>

    </div>
  </section>

  <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
    <RouterLink to="/jobs" class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700">
      View All Jobs</RouterLink>
  </section>
</template>