<template>
  <div class="wrapper flex justify-center py-14 font-body">
    <div v-if="isLoading" class="loader"><app-loader /></div>
    <app-error v-else-if="statusError" :message="errorMessage" />
    <div v-else class="w-8/12 md:w-1/2 lg:w-1/3 flex items-center flex-col">
      <!-- main card -->
      <div class="w-full relative mb-9">
        <input
          class="w-full rounded-full py-2 pl-3 bg-slate-100 focus:outline-none shadow-lg focus:shadow-sm"
          type="text"
          placeholder="Enter your city"
          v-model="city"
        />
        <div
          class="absolute flex justify-center items-center top-1/2 right-0 transform -translate-y-1/2 rounded-full bg-yale h-full w-10"
        >
          <button @click="fetchDataByCity">
            <fa icon="search" class="text-slate-100" />
          </button>
        </div>
        <div v-if="isInvalidInput" class="absolute text-sm text-red-500">
          {{ errorMessage }}
        </div>
      </div>
      <h1 class="text-center text-3xl text-mangolia font-semibold mt-8 mb-7">
        {{ allData.name }}, {{ allData.sys.country }}
      </h1>
      <img class="w-1/2 mb-6" :src="image" alt="picture" />
      <p class="font-extrabold text-7xl text-degree mb-4">{{ temp }} °C</p>
      <p class="text-center text-2xl text-mangolia font-semibold">
        {{ allData.weather[0].main }}
      </p>
    </div>
  </div>
</template>

<script>
import AppError from "@/components/AppError.vue";
import AppLoader from "@/components/AppLoader";

export default {
  name: "App",
  components: {
    AppError,
    AppLoader,
  },
  data() {
    return {
      allData: null,
      isLoading: true,
      statusError: false,
      isInvalidInput: false,
      errorMessage: "",
      city: "",
    };
  },
  computed: {
    temp() {
      return Math.floor(this.allData.main.temp);
    },
    image() {
      return `./weather/${this.allData.weather[0].icon}.svg`;
    },
  },
  methods: {
    async fetchData(position) {
      this.isLoading = true;
      try {
        const lat = position.coords.latitude;
        const lon = position.coords.longitude;
        const apiKey = "13e1400ec7b3478c6402577af304f670";
        const response = await fetch(
          `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${apiKey}&units=metric`
        );
        this.allData = await response.json();
      } catch (e) {
        console.log(e);
      } finally {
        this.isLoading = false;
      }
    },

    async fetchDataByCity() {
      if (this.city) {
        this.isLoading = true;
        this.isInvalidInput = false;
        try {
          const apiKey = "13e1400ec7b3478c6402577af304f670";
          const response = await fetch(
            `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${apiKey}&units=metric`
          );
          if (response.ok) {
            this.allData = await response.json();
          } else {
            const error = await response.json();
            throw new Error(error.message);
          }
        } catch (error) {
          this.isInvalidInput = true;
          this.errorMessage = error.message;
        } finally {
          this.isLoading = false;
        }
      } else {
        return;
      }
    },
  },

  mounted() {
    navigator.geolocation.getCurrentPosition(this.fetchData, () => {
      this.isLoading = false;
      this.statusError = true;
      this.errorMessage =
        "You need to provide access to geolocation in order to use the app. You can do this in your browser settings.";
    });
  },
};
</script>

<style></style>
