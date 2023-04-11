<script setup>
import { ref } from 'vue';
import { NUM_FLOORS, NUM_ELEVATORS } from '../../config';

const numFloors = ref(NUM_FLOORS);
const numElevators = ref(NUM_ELEVATORS);

const validateValue = (value) => {
  return value > 0 ? value : 1;
};

const saveConfig = () => {
  localStorage.setItem('numFloors', numFloors.value);
  localStorage.setItem('numElevators', numElevators.value);
};

const updateConfig = () => {
  numFloors.value = validateValue(numFloors.value);
  numElevators.value = validateValue(numElevators.value);
  saveConfig();
  location.reload();
};
</script>

<template>
  <div class="settings">
    <h3 class="settings-header">Settings</h3>
    <div>
      <label for="numFloors" class="settings-label">Number of floors:</label>
      <input
        class="settings-input"
        type="number"
        id="numFloors"
        v-model="numFloors"
        min="1"
        @change="updateConfig"
      />
    </div>
    <div>
      <label for="numElevators" class="settings-label"
        >Number of elevators:</label
      >
      <input
        class="settings-input"
        type="number"
        id="numElevators"
        v-model="numElevators"
        min="1"
        @change="updateConfig"
      />
    </div>
  </div>
</template>

<style src="./SettingsPanel.css" scoped></style>
