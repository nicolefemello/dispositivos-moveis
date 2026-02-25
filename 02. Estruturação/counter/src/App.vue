<script setup>
import { ref } from 'vue';

const count = ref(0);

function increment() {
  count.value++;
}

function decrement() {
  count.value--;
}

function reset() {
  count.value = 0;
}
</script>

<template>
  <div class="counter">
    <h1>Contador</h1>
    <div class="display">{{ count }}</div>
    <div class="buttons">
      <button @click="decrement" class="btn">-</button>
      <button @click="reset" class="btn btn-reset">Reset</button>
      <button @click="increment" class="btn">+</button>
    </div>
  </div>
</template>

<style scoped>
.counter {
  padding: 20px;
  text-align: center;
}

.display {
  font-size: 72px;
  font-weight: bold;
  margin: 40px 0;
  color: #0b5cff;
}

.buttons {
  display: flex;
  gap: 10px;
  justify-content: center;
}

.btn {
  min-width: 60px;
  min-height: 60px;
  font-size: 24px;
  border: none;
  border-radius: 8px;
  background: #0b5cff;
  color: white;
  font-weight: bold;
  cursor: pointer;
  transition: transform 0.1s;
}

.btn:active {
  transform: scale(0.95);
}

.btn-reset {
  background: #ff4444;
}
</style>