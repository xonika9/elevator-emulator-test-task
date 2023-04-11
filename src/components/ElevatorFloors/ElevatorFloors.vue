<script setup>
const props = defineProps({
  callElevator: Function,
  activeCalls: Array,
  arrivedFloors: Array,
  numFloors: Number,
});
</script>

<template>
  <div class="floors">
    <div v-for="floor in props.numFloors" :key="floor" class="floor">
      <div class="floor-number">{{ floor }}</div>
      <button
        class="call-button"
        :class="{ active: activeCalls[floor - 1] && !arrivedFloors[floor - 1] }"
        @click="callElevator(floor)"
      >
        Call
      </button>
    </div>
  </div>
</template>

<style src="./ElevatorFloors.css" scoped></style>
