<script setup>
import { computed } from 'vue';
import IndicationPanel from '../IndicationPanel/IndicationPanel.vue';

const props = defineProps({
  currentFloor: Number,
});

const cabStyle = computed(() => {
  const floorHeight = 100;
  const translateY = (props.currentFloor - 1) * floorHeight;
  return {
    transform: `translateY(-${translateY}%)`,
  };
});
</script>

<template>
  <div class="elevator-cab" :style="cabStyle">
    <indication-panel :currentFloor="props.currentFloor" />
  </div>
</template>

<style src="./ElevatorCab.css" scoped></style>
