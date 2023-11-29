<template>
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
</template>

<script setup>
import { IonItem, IonSelect, IonSelectOption } from "@ionic/vue";
import { ref } from "vue";

defineProps({
  cities: {
    type: Array,
    required: true,
  }
});

const city = ref("PA");

function removeAccents(str) {
  return str.normalize("NFD").replace(/[\u0300-\u036f]/g, "");
}
</script>

<style lang="scss" scoped>
</style>