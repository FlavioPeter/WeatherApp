<template>
  <ion-app>
    <HeaderComp />
    <ion-router-outlet />
    <FooterComp />
  </ion-app>
</template>

<script setup lang="ts">
import HeaderComp from "@/components/HeaderComp.vue";
import FooterComp from "@/components/FooterComp.vue";
import { IonApp, IonRouterOutlet } from "@ionic/vue";
</script>

