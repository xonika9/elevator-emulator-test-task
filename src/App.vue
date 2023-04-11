<script setup>
import { ref, reactive, watch, computed } from 'vue';
import ElevatorShaft from './components/ElevatorShaft/ElevatorShaft.vue';
import ElevatorFloors from './components/ElevatorFloors/ElevatorFloors.vue';
import ElevatorCab from './components/ElevatorCab/ElevatorCab.vue';
import { NUM_FLOORS, NUM_ELEVATORS } from './config';

const createElevator = () => {
  const elevator = {
    currentFloor: ref(1),
    targetFloor: reactive({ value: 1 }),
    direction: ref(''),
    activeCalls: reactive(new Array(NUM_FLOORS).fill(false)),
    arrivedFloors: reactive(new Array(NUM_FLOORS).fill(false)),
    callQueue: ref([]),
    movingToTarget: ref(false),
  };

  watch(elevator.currentFloor, (newFloor, oldFloor) => {
    elevator.direction.value = newFloor > oldFloor ? '↑' : '↓';
  });

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

const restoreState = (elevator, index) => {
  const storedState = JSON.parse(
    localStorage.getItem(`elevatorState-${index}`) || '{}'
  );

  elevator.currentFloor.value = storedState.currentFloor || 1;
  elevator.targetFloor.value = storedState.targetFloor || 1;
  elevator.callQueue.value = storedState.callQueue || [];
};

const operateElevator = (elevator, index) => {
  restoreState(elevator, index);

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
      const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

      while (elevator.currentFloor.value !== elevator.targetFloor.value) {
        if (elevator.targetFloor.value > elevator.currentFloor.value) {
          elevator.currentFloor.value++;
        } else {
          elevator.currentFloor.value--;
        }
        await delay(1000);
      }

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
  const elevator = createElevator();
  const callElevator = operateElevator(elevator, index);
  return { ...elevator, callElevator };
});

const callElevator = async (floor) => {
  const availableElevator = elevators.find(
    (elevator) => !elevator.movingToTarget.value
  );
  const elevatorToUse = availableElevator || elevators[0];
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
