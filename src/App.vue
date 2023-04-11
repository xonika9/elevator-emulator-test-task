<script setup>
import { ref, reactive, watch } from 'vue';
import ElevatorShaft from './components/ElevatorShaft/ElevatorShaft.vue';
import ElevatorCab from './components/ElevatorCab/ElevatorCab.vue';

const currentFloor = ref(1);
const targetFloor = reactive({ value: 1 });
const direction = ref('');

const callElevator = async (floor) => {
  targetFloor.value = floor;
  const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

  while (currentFloor.value !== floor) {
    if (floor > currentFloor.value) {
      currentFloor.value++;
    } else {
      currentFloor.value--;
    }
    await delay(1000);
  }
};

watch(currentFloor, (newFloor, oldFloor) => {
  direction.value = newFloor > oldFloor ? '↑' : '↓';
});
</script>

<template>
  <elevator-shaft
    :callElevator="callElevator"
    :targetFloor="targetFloor.value"
    :currentFloor="currentFloor"
  />
  <elevator-cab
    :currentFloor="currentFloor"
    :direction="direction"
    :targetFloor="targetFloor.value"
  />
</template>

<style scoped></style>
