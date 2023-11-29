<template>
  <ion-page>
    <HeaderComp />

    <ion-content :fullscreen="true">
      <div id="container">
        <div>
          <DateDisplayComp />
        </div>
        <ion-item>
          <ion-select label="Ville" placeholder="Choisir" v-model="city">
            <ion-select-option
              v-for="c in cities"
              :key="c.id"
              :value="removeAccents(c.name)"
              >{{ c.name }}</ion-select-option
            >
            <ion-select-option value="PA">Position actuelle</ion-select-option>
          </ion-select>
        </ion-item>
        <WeatherDisplayComp :weather="weather" />
      </div>
    </ion-content>

    <FooterComp />
  </ion-page>
</template>

<script setup>
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonFooter,
  IonBackButton,
  IonButtons,
  IonLabel,
  IonItem,
  IonInput,
  IonSelect,
  IonSelectOption,
} from "@ionic/vue";

import HeaderComp from "@/components/HeaderComp.vue";
import DateDisplayComp from "@/components/DateDisplayComp.vue";
// import LocationInputComp from "@/components/LocationInputComp.vue";
import WeatherDisplayComp from "@/components/WeatherDisplayComp.vue";
import FooterComp from "@/components/FooterComp.vue";
import { onMounted, reactive, ref, watch } from "vue";

const apiKey = "4d8cfd4d6529e123c6d98b6f14dd977f"; // Replace with your actual API key
const lat = ref(0);
const lng = ref(0);
const cities = ref([
  { id: 6077243, name: "Montréal" },
  { id: 6050610, name: "Laval" },
  { id: 6325494, name: "Québec" },
]);
const city = ref("PA");

const weather = reactive({
  name: "",
  temp: "",
  icon: "",
  description: "",
});

function removeAccents(str) {
  return str.normalize("NFD").replace(/[\u0300-\u036f]/g, "");
}

function getPosition() {
  if (navigator.geolocation) {
    navigator.geolocation.watchPosition((position) => {
      lat.value = position.coords.latitude;
      lng.value = position.coords.longitude;
    });
  }
}

async function getWeather() {
  let apiUrl = "";
  if (city.value === "PA") {
    apiUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${lat.value}&lon=${lng.value}&units=metric&lang=fr&appid=${apiKey}`;
  } else {
    apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&lang=fr&appid=${apiKey}`;
  }
  console.log(apiUrl);
  //Fetch data from the API
  fetch(apiUrl)
    .then((response) => {
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      return response.json();
    })
    .then((data) => {
      // Handle the retrieved weather data
      weather.name = data.name;
      weather.temp = data.main.temp;
      weather.icon = data.weather[0].icon;
      weather.description = data.weather[0].description;
    })
    .catch((error) => {
      // Handle errors
      console.error("Error fetching weather data:", error);
    });
}

// watch(city, getWeather);
// getPosition();

onMounted(() => {
  getPosition();
  watch([city, lat, lng], getWeather);
});
</script>

<style scoped>
/* #container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  top: 350px;
  transform: translateY(-50%);
} */

#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  top: 350px;
  transform: translateY(-50%);
}

</style>
