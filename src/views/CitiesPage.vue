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
  onIonViewWillEnter,
} from "@ionic/vue";
import { onMounted, ref, watch } from "vue";
import { useToast } from "vue-toastification";

const apiKey = import.meta.env.VITE_OW_API_KEY;
const cities = ref([]);
const toast = useToast();

function restoreCities() {
  const savedCities = JSON.parse(localStorage.getItem("weatherCities"));
  if (savedCities) {
    cities.value = savedCities;
  }
}

function backupCities() {
  localStorage.setItem("weatherCities", JSON.stringify(cities.value));
}

async function getCity(name) {
  const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${name}&units=metric&lang=fr&appid=${apiKey}`;

  console.log(apiUrl);

  return fetch(apiUrl).then((response) => {
    if (!response.ok) {
      return response.status;
    }
    return response.json();
  });
}

async function addCity(name) {
  const data = await getCity(name);
  if (data === 404 || data === 400) {
    toast.error(`Ville pas trouvé`);
  } else if (cities.value.some((c) => data.id === c.id)) {
    toast.warning(`${data.name} est déjà sur la liste`);
    console.log("City already exists");
  } else {
    // Add logic to actually add the city to the cities array
    cities.value.push({
      id: data.id,
      name: data.name,
    });

    toast.success(`${data.name} a été ajoutée`);

    backupCities();
  }
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

onIonViewWillEnter(() => {
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