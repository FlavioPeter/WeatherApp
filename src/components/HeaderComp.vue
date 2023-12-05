<template>
  <ion-header :translucent="true">
    <ion-toolbar id="appHeader" color="primary">
      <ion-title size="large">{{
        $route.name == "Home" ? "Ma météo" : "Villes"
      }}</ion-title>
    </ion-toolbar>
  </ion-header>
</template>

<script setup>
import { IonHeader, IonTitle, IonToolbar } from "@ionic/vue";
</script>

<style scoped>
#appHeader {
  display: flex;
}
</style>