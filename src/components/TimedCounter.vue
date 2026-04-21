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