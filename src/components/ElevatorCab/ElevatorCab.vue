<script setup>
import { computed } from 'vue';
import IndicationPanel from '../IndicationPanel/IndicationPanel.vue';

const props = defineProps({
  currentFloor: Number,
  direction: String,
  targetFloor: Number,
  activeCalls: Array,
  numFloors: Number,
});

const isResting = computed(() => props.activeCalls[props.currentFloor - 1]);

const cabStyle = computed(() => {
  const floorHeight = 100;
  const translateY = (props.currentFloor - 1) * floorHeight;
  const transitionDuration = 1;
  const cabHeight = 100 / props.numFloors;
  return {
    transform: `translateY(-${translateY}%)`,
    transition: `transform ${transitionDuration}s linear`,
    height: `${cabHeight}%`,
  };
});
</script>

<template>
  <div class="elevator-cab" :class="{ resting: isResting }" :style="cabStyle">
    <indication-panel
      :currentFloor="props.targetFloor"
      :direction="props.direction"
    />
  </div>
</template>

<style src="./ElevatorCab.css" scoped></style>
