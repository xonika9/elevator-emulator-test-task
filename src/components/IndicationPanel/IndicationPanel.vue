<script setup>
const props = defineProps({
  currentFloor: Number,
});
</script>

<template>
  <div class="indication-panel">
    <div class="direction">up</div>
    <div class="target-floor">{{ currentFloor }}</div>
  </div>
</template>

<style src="./IndicationPanel.css" scoped></style>
