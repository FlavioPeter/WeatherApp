<template>
  <div id="display">
    <h1>{{ weather.name }}</h1>
    <h2>{{ weather.temp }}&deg;C</h2>
    <ion-img
      :src="'/src/icons/' + weather.icon + '@2x.png'"
      alt="Description of the image"
    ></ion-img>
    <p>{{ weather.description }}</p>
  </div>
</template>

<script setup>
import { IonImg } from "@ionic/vue";

defineProps({
  weather: {
    type: Object,
    required: true,
  },
});
</script>

<style scoped>
ion-img {
  width: 300px;
}

#display {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-bottom: 75px;
}
</style>