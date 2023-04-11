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
const callQueue = ref([]);
const movingToTarget = ref(false);

const callElevator = async (floor) => {
  if (
    (currentFloor.value === floor && !movingToTarget.value) ||
    (targetFloor.value === floor && movingToTarget.value) ||
    callQueue.value.includes(floor)
  ) {
    return;
  }

  callQueue.value.push(floor);
  activeCalls[floor - 1] = true;
  if (movingToTarget.value) return;

  while (callQueue.value.length > 0) {
    movingToTarget.value = true;
    targetFloor.value = callQueue.value[0];
    activeCalls[targetFloor.value - 1] = true;
    const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

    while (currentFloor.value !== targetFloor.value) {
      if (targetFloor.value > currentFloor.value) {
        currentFloor.value++;
      } else {
        currentFloor.value--;
      }
      await delay(1000);
    }

    arrivedFloors[targetFloor.value - 1] = true;
    await delay(3000);
    activeCalls[targetFloor.value - 1] = false;
    arrivedFloors[targetFloor.value - 1] = false;
    callQueue.value.shift();
    movingToTarget.value = false;
  }
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
