<script setup>
import { ref, reactive } from 'vue';
import ElevatorShaft from './components/ElevatorShaft/ElevatorShaft.vue';
import ElevatorCab from './components/ElevatorCab/ElevatorCab.vue';

const currentFloor = ref(1);
const targetFloor = reactive({ value: 1 });

const callElevator = (floor) => {
  targetFloor.value = floor;
  currentFloor.value = floor;
};
</script>

<template>
  <elevator-shaft :callElevator="callElevator" />
  <elevator-cab :currentFloor="currentFloor" />
</template>

<style scoped></style>
