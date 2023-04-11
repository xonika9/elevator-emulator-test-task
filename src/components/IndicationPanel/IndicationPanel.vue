<script setup>
const props = defineProps({
  currentFloor: Number,
  direction: String,
});
</script>

<template>
  <div class="indication-panel">
    <div class="direction">{{ direction }}</div>
    <div class="target-floor">{{ currentFloor }}</div>
  </div>
</template>

<style src="./IndicationPanel.css" scoped></style>
