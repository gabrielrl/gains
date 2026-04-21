<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const unit = "$";

const count = ref(0)
const velocity = ref(0)
const velocityInput = ref(velocity.value)

function applyVelocity() {
  velocity.value = velocityInput.value
}

let timer
onMounted(() => { timer = setInterval(() => { count.value += velocity.value }, 1000) })
onUnmounted(() => clearInterval(timer))
</script>

<template>
  <main>
    <h1>Timed Counter</h1>
    <p class="count">{{ count }} {{ unit }}</p>
    <div class="velocity-input">
      <label for="velocity-field">Taux</label>
      <input
        id="velocity-field"
        v-model="velocityInput"
        type="number"
        min="0"
        step="1"
        @change="applyVelocity"
      />
      <div>{{ unit }}/s</div>
    </div>
    <div class="actions">
      <button @click="velocity--">-</button>
      <button @click="count = 0; velocity = 0">Reset</button>
      <button @click="velocity++">+</button>
    </div>
  </main>
</template>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: system-ui, sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: #f5f5f5;
}

main {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
}

h1 {
  font-size: 1.5rem;
  color: #333;
}

.count {
  font-size: 5rem;
  font-weight: bold;
  color: #111;
  min-width: 4ch;
}

.actions {
  display: flex;
  gap: 0.75rem;
}

button {
  font-size: 1.25rem;
  padding: 0.5rem 1.25rem;
  border: 2px solid #333;
  border-radius: 6px;
  background: white;
  cursor: pointer;
  transition: background 0.15s;
}

button:hover {
  background: #333;
  color: white;
}

.velocity-input {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 0.4rem;
}

.velocity-input label {
  font-size: 0.875rem;
  color: #555;
}

.velocity-input input {
  font-size: 1.1rem;
  padding: 0.4rem 0.75rem;
  width: 8ch;
  text-align: center;
  border: 2px solid #333;
  border-radius: 6px;
  outline: none;
}

</style>
