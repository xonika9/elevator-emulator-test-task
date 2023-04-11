<script setup>
const props = defineProps({
  callElevator: Function,
});
</script>

<template>
  <div class="elevator-shaft"></div>
  <div class="floors">
    <div v-for="floor in 5" :key="floor" class="floor">
      <div class="floor-number">{{ floor }}</div>
      <button class="call-button" @click="() => callElevator(floor)">
        Call
      </button>
    </div>
  </div>
</template>

<style src="./ElevatorShaft.css" scoped></style>
