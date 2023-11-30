<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <ion-list id="container">
        <ion-buttons>
          <ion-button class="add-city-btn" @click="addCityPopup"
            >Ajouter une nouvelle ville</ion-button
          >
        </ion-buttons>
        <ion-item v-for="c in cities" :key="c.id"
          >{{ c.name }}
          <ion-buttons slot="end"
            ><ion-button
              class="remove-city-btn"
              @click="deleteCity(c.id)"
              size="large"
              shape="round"
              >-</ion-button
            ></ion-buttons
          ></ion-item
        >
      </ion-list>
    </ion-content>
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
  IonList,
  IonIcon,
  IonTabs,
  IonTabButton,
  alertController,
} from "@ionic/vue";
import { onMounted, ref, watch } from "vue";

const apiKey = import.meta.env.VITE_OW_API_KEY;
const cities = ref([]);

function restoreCities() {
  const savedCities = JSON.parse(localStorage.getItem("weatherCities"));
  if (savedCities) {
    cities.value = savedCities;
  }
}

function backupCities() {
  localStorage.setItem("weatherCities", JSON.stringify(cities.value));
}

async function addCity(name) {
  const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${name}&units=metric&lang=fr&appid=${apiKey}`;

  console.log(apiUrl);

  fetch(apiUrl)
    .then((response) => {
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      return response.json();
    })
    .then((data) => {
      // Handle the retrieved city data
      cities.value.push({
        id: data.id,
        name: data.name,
      });
      backupCities();
    })
    .catch((error) => {
      // Handle errors
      console.error("Error fetching weather data:", error);
    });
}

async function addCityPopup() {
  const add = await alertController.create({
    header: "Ajouter",
    inputs: [
      {
        name: "text",
        type: "text",
        placeholder: "Nom de la ville a ajouter",
      },
    ],
    buttons: [
      {
        text: "OK",
        handler: (newCity) => addCity(newCity.text),
      },
      {
        text: "Annuler",
        handler: () => console.log("Annuler"),
      },
    ],
  });

  await add.present();
}

function deleteCity(cityId) {
  cities.value = cities.value.filter((c) => c.id !== cityId);
}

onMounted(() => {
  restoreCities();
  watch(cities, backupCities, { deep: true });
});
</script>

<style>
#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  padding-top: 75px;
}

.add-city-btn {
  text-align: center;
  width: 100%;
  background: green;
  border: solid;
  border-radius: 10px;
}

.remove-city-btn {
  background: red;
  border: solid;
  border-radius: 100%;
}
</style>