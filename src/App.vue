<script setup></script>

<template>
  <div class="elevator-shaft"></div>
  <div class="floors">
    <div v-for="floor in 5" :key="floor" class="floor">
      <div class="floor-number">{{ floor }}</div>
      <button class="call-button">Call</button>
    </div>
  </div>
  <div class="elevator-cab">
    <div class="indication-panel">
      <div class="direction">up</div>
      <div class="target-floor">5</div>
    </div>
  </div>
</template>

<style scoped>
.elevator-shaft {
  box-sizing: border-box;
  padding: 1rem;
  width: 100px;
  height: 100%;
  border-right: 2px solid #dcdcdc;
  background-color: #f0f0f0;
}

.floors {
  display: flex;
  align-items: center;
  flex-direction: column-reverse;
  justify-content: space-around;
}

.floor {
  display: flex;
  align-items: center;
  box-sizing: border-box;
  padding: 0.5rem;
  width: 100%;
  height: 20%;
}

.floor-number {
  color: #333;
  font-weight: bold;
  font-size: 1.5rem;
}

.call-button {
  padding: 0.5rem;
  border: none;
  border-radius: 0.25rem;
  background-color: #333;
  color: #fff;
  font-weight: bold;
  font-size: 1rem;
  cursor: pointer;
}

.call-button.active {
  background-color: #f90;
}

.elevator-cab {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100px;
  height: 20%;
  background-color: #80c8e3;
  transition: transform 1s linear;
}

.indication-panel {
  display: flex;
  justify-content: center;
  gap: 5px;
  margin: 1rem;
  border-radius: 5px;
  background-color: white;
}
</style>
