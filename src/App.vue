<script setup>
import { ref, reactive, watch } from 'vue';
import ElevatorShaft from './components/ElevatorShaft/ElevatorShaft.vue';
import ElevatorCab from './components/ElevatorCab/ElevatorCab.vue';

const currentFloor = ref(1);
const targetFloor = reactive({ value: 1 });
const direction = ref('');

const callElevator = (floor) => {
  targetFloor.value = floor;
  currentFloor.value = floor;
};

watch(currentFloor, (newFloor, oldFloor) => {
  direction.value = newFloor > oldFloor ? '↑' : '↓';
});
</script>

<template>
  <elevator-shaft :callElevator="callElevator" />
  <elevator-cab :currentFloor="currentFloor" :direction="direction" />
</template>

<style scoped></style>
