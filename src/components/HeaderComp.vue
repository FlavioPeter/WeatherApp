<template>
  <ion-header :translucent="true">
    <ion-toolbar id="appHeader" color="primary">
      <ion-buttons v-show="$route.name === 'Cities'" slot="start">
        <ion-back-button default-href="/" />
      </ion-buttons>
      <ion-title size="large">{{
        $route.name == "Home" ? "Ma météo" : "Villes"
      }}</ion-title>
      <ion-buttons v-if="$route.name === 'Home'" slot="end">
        <ion-button href="/cities" shape="round">
          <ion-menu-button auto-hide="false"></ion-menu-button>
        </ion-button>
        <ion-button shape="round" size="large" v-show="$route.name === 'Cities'"
          >+</ion-button
        >
      </ion-buttons>
      <ion-buttons v-else slot="end">
        <ion-button shape="round" size="large">+</ion-button>
      </ion-buttons>
    </ion-toolbar>
  </ion-header>
</template>

<script setup>
import {
  IonHeader,
  IonTitle,
  IonToolbar,
  IonButton,
  IonButtons,
  IonBackButton,
  IonMenuButton,
} from "@ionic/vue";
</script>

<style scoped>
#appHeader {
  display: flex;
}

ion-buttons {
}
</style>