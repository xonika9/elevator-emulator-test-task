<script setup>
import { ref, reactive, computed } from 'vue';
import ElevatorShaft from './components/ElevatorShaft/ElevatorShaft.vue';
import ElevatorFloors from './components/ElevatorFloors/ElevatorFloors.vue';
import ElevatorCab from './components/ElevatorCab/ElevatorCab.vue';
import { NUM_FLOORS, NUM_ELEVATORS } from './config';

const elevatorState = (index) => {
  const storedState = JSON.parse(
    localStorage.getItem(`elevatorState-${index}`) || '{}'
  );
  return {
    currentFloor: ref(storedState.currentFloor || 1),
    targetFloor: ref(storedState.targetFloor || 1),
    callQueue: ref(storedState.callQueue || []),
  };
};

const createElevator = (index) => {
  const state = elevatorState(index);
  const elevator = {
    ...state,
    direction: ref(''),
    activeCalls: reactive(new Array(NUM_FLOORS).fill(false)),
    arrivedFloors: reactive(new Array(NUM_FLOORS).fill(false)),
    movingToTarget: ref(false),
  };
  return elevator;
};

const saveState = (elevator, index) => {
  const state = {
    currentFloor: elevator.currentFloor.value,
    targetFloor: elevator.targetFloor.value,
    callQueue: elevator.callQueue.value,
  };
  localStorage.setItem(`elevatorState-${index}`, JSON.stringify(state));
};

const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

// Перемещение лифта к целевому этажу
const moveElevatorToTarget = async (elevator) => {
  while (elevator.currentFloor.value !== elevator.targetFloor.value) {
    elevator.direction.value =
      elevator.targetFloor.value > elevator.currentFloor.value ? '↑' : '↓';
    elevator.currentFloor.value += elevator.direction.value === '↑' ? 1 : -1;
    await delay(1000);
  }
};

const operateElevator = (elevator, index) => {
  const callElevator = async (floor) => {
    if (
      (elevator.currentFloor.value === floor &&
        !elevator.movingToTarget.value) ||
      (elevator.targetFloor.value === floor && elevator.movingToTarget.value) ||
      elevator.callQueue.value.includes(floor)
    ) {
      return;
    }

    elevator.callQueue.value.push(floor);
    elevator.activeCalls[floor - 1] = true;
    saveState(elevator, index);

    if (elevator.movingToTarget.value) return;
    while (elevator.callQueue.value.length > 0) {
      elevator.movingToTarget.value = true;
      elevator.targetFloor.value = elevator.callQueue.value[0];
      elevator.activeCalls[elevator.targetFloor.value - 1] = true;

      await moveElevatorToTarget(elevator);

      elevator.arrivedFloors[elevator.targetFloor.value - 1] = true;
      await delay(3000);
      elevator.activeCalls[elevator.targetFloor.value - 1] = false;
      elevator.arrivedFloors[elevator.targetFloor.value - 1] = false;
      elevator.callQueue.value.shift();
      elevator.movingToTarget.value = false;
      saveState(elevator, index);
    }
  };

  return callElevator;
};

const elevators = new Array(NUM_ELEVATORS).fill(null).map((_, index) => {
  const elevator = createElevator(index);
  const callElevator = operateElevator(elevator, index);
  return { ...elevator, callElevator };
});

const callElevator = async (floor) => {
  const availableElevators = elevators.filter(
    (elevator) => !elevator.movingToTarget.value
  );

  const closestElevator = availableElevators.reduce((closest, elevator) => {
    if (
      !closest ||
      Math.abs(elevator.currentFloor.value - floor) <
        Math.abs(closest.currentFloor.value - floor)
    ) {
      return elevator;
    }
    return closest;
  }, null);

  const elevatorToUse = closestElevator || elevators[0];
  await elevatorToUse.callElevator(floor);
};

const allActiveCalls = computed(() => {
  return elevators
    .map((elevator) => elevator.activeCalls)
    .reduce((accumulator, calls) => {
      return accumulator.map((active, index) => active || calls[index]);
    });
});
</script>

<template>
  <div class="elevator-shafts">
    <elevator-shaft v-for="(elevator, index) in elevators" :key="index" />
  </div>
  <elevator-floors
    :callElevator="callElevator"
    :activeCalls="allActiveCalls"
    :arrivedFloors="elevators[0].arrivedFloors"
    :numFloors="NUM_FLOORS"
  />
  <div class="elevator-cabs">
    <elevator-cab
      v-for="(elevator, index) in elevators"
      :key="index"
      :currentFloor="elevator.currentFloor.value"
      :direction="elevator.direction.value"
      :targetFloor="elevator.targetFloor.value"
      :activeCalls="elevator.activeCalls"
      :numFloors="NUM_FLOORS"
    />
  </div>
</template>

<style scoped>
.elevator-shafts {
  display: flex;
  align-items: flex-end;
}

.elevator-cabs {
  position: absolute;
  bottom: 0;
  left: 0;
  display: flex;
  align-items: flex-end;
  height: 100%;
}
</style>
