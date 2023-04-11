<script setup>
import { computed } from 'vue';
import IndicationPanel from '../IndicationPanel/IndicationPanel.vue';

const props = defineProps({
  currentFloor: Number,
  direction: String,
  targetFloor: Number,
  activeCalls: Array,
});

const isResting = computed(() => props.activeCalls[props.currentFloor - 1]);

const cabStyle = computed(() => {
  const floorHeight = 100;
  const translateY = (props.currentFloor - 1) * floorHeight;
  const transitionDuration = 1; // одна секунда на этаж
  return {
    transform: `translateY(-${translateY}%)`,
    transition: `transform ${transitionDuration}s linear`,
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
