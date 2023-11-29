<template>
  <ion-title size="small">{{ todaysDate }}</ion-title>
</template>

<script setup>
import { IonTitle } from "@ionic/vue";

import { computed, ref } from "vue";

// Get the current date
const currentDate = ref(new Date());

const todaysDate = computed(() => {
  // Create a date formatter for French locale
  const dateFormatter = new Intl.DateTimeFormat("fr-FR", {
    weekday: "long",
    day: "numeric",
    month: "long",
    year: "numeric",
  });

  // Format the date
  const dateComponents = dateFormatter.format(currentDate.value).split(" ");

  const weekday =
    dateComponents[0].charAt(0).toUpperCase() + dateComponents[0].slice(1);
  const day = dateComponents[1];
  const month = dateComponents[2];
  const year = dateComponents[3];

  return `${weekday}, ${day} ${month} ${year}`;
});
</script>

<style scoped>
</style>