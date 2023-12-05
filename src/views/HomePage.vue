<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <div id="container">
        <DateDisplayComp />

        <ion-select label="Ville" placeholder="Choisir" v-model="city">
          <ion-select-option
            v-for="c in cities"
            :key="c.id"
            :value="removeAccents(c.name)"
            >{{ c.name }}</ion-select-option
          >
          <ion-select-option value="PA">Position actuelle</ion-select-option>
        </ion-select>

        <WeatherDisplayComp v-show="weather.icon" :weather="weather" />
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup>
import {
  IonContent,
  IonPage,
  IonSelect,
  IonSelectOption,
  onIonViewWillEnter,
  onIonViewDidEnter,
  loadingController,
} from "@ionic/vue";
import { Geolocation } from "@capacitor/geolocation";
import DateDisplayComp from "@/components/DateDisplayComp.vue";
import WeatherDisplayComp from "@/components/WeatherDisplayComp.vue";
import { reactive, ref, watch } from "vue";

const apiKey = import.meta.env.VITE_OW_API_KEY;
const lat = ref(0);
const lng = ref(0);
const cities = ref([
  { id: 6077243, name: "Montréal" },
  { id: 6050610, name: "Laval" },
  { id: 6325494, name: "Québec" },
]);
const city = ref("PA");

const weather = reactive({
  name: null,
  temp: null,
  icon: null,
  description: null,
});

function removeAccents(str) {
  return str.normalize("NFD").replace(/[\u0300-\u036f]/g, "");
}

async function getPosition() {
  try {
    const position = await Geolocation.getCurrentPosition();
    lat.value = position.coords.latitude;
    lng.value = position.coords.longitude;
  } catch (error) {
    return;
  }
}

async function watchPosition() {
  await getPosition();
  Geolocation.watchPosition(
    () => {
      getWeather();
    },
    () => {
      getWeather();
    },
    { enableHighAccuracy: false }
  );
}

async function getWeather() {
  let apiUrl = "";
  if (city.value === "PA") {
    await getPosition();
    apiUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${lat.value}&lon=${lng.value}&units=metric&lang=fr&appid=${apiKey}`;
  } else {
    apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&lang=fr&appid=${apiKey}`;
  }
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

onIonViewWillEnter(async () => {
  const loading = await loadingController.create({
    message: "Attendre SVP...",
  });

  await loading.present();
  await getWeather();
  loading.dismiss();
});

onIonViewDidEnter(() => {
  watchPosition();
  watch(city, getWeather);
});
</script>

<style scoped>
#container {
  text-align: center;
  position: absolute;
  left: 5%;
  right: 5%;
  padding-top: 75px;
}

ion-select {
  border-bottom: solid;
  border-width: 1px;
  border-color: gray;
}
</style>
