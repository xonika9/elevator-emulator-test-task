<script setup>
import { ref, reactive, watch } from 'vue';
import ElevatorShaft from './components/ElevatorShaft/ElevatorShaft.vue';
import ElevatorCab from './components/ElevatorCab/ElevatorCab.vue';
import { NUM_FLOORS } from './config';

const currentFloor = ref(1);
const targetFloor = reactive({ value: 1 });
const direction = ref('');
const activeCalls = reactive(new Array(NUM_FLOORS).fill(false));
const arrivedFloors = reactive(new Array(NUM_FLOORS).fill(false));

const callElevator = async (floor) => {
  targetFloor.value = floor;
  activeCalls[floor - 1] = true;
  const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

  while (currentFloor.value !== floor) {
    if (floor > currentFloor.value) {
      currentFloor.value++;
    } else {
      currentFloor.value--;
    }
    await delay(1000);
  }

  arrivedFloors[floor - 1] = true;
  await delay(3000);
  activeCalls[floor - 1] = false;
  arrivedFloors[floor - 1] = false;
};

watch(currentFloor, (newFloor, oldFloor) => {
  direction.value = newFloor > oldFloor ? '↑' : '↓';
});
</script>

<template>
  <elevator-shaft
    :callElevator="callElevator"
    :activeCalls="activeCalls"
    :arrivedFloors="arrivedFloors"
    :numFloors="NUM_FLOORS"
  />
  <elevator-cab
    :currentFloor="currentFloor"
    :direction="direction"
    :targetFloor="targetFloor.value"
    :activeCalls="activeCalls"
    :numFloors="NUM_FLOORS"
  />
</template>

<style scoped></style>
