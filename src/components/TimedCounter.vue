<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const unit = "$";

const SUFFIX_LIST = [
  ['k', 1e3], ['M', 1e6], ['G', 1e9], ['T', 1e12],
  ['P', 1e15], ['E', 1e18], ['Z', 1e21], ['Y', 1e24], ['R', 1e27], ['Q', 1e30]
]

const count = ref(0)
const velocity = ref(0)
const velocityInput = ref(velocity.value)
const velocitySuffix = ref('')

function applyVelocity() {
  const multiplier = SUFFIX_LIST.find(([s]) => s === velocitySuffix.value)?.[1] ?? 1
  velocity.value = velocityInput.value * multiplier
}

const formattedCount = computed(() => {
  const n = count.value
  const abs = Math.abs(n)
  const sign = n < 0 ? '-' : ''
  for (const [symbol, value] of [...SUFFIX_LIST].reverse()) {
    if (abs >= value) return sign + parseFloat((abs / value).toFixed(3)) + symbol
  }
  return String(n)
})

let timer
onMounted(() => { timer = setInterval(() => { count.value += velocity.value }, 1000) })
onUnmounted(() => clearInterval(timer))
</script>

<template>
  <div class="timed-counter">
    <h1>Timed Counter</h1>
    <div class="count">{{ formattedCount }} {{ unit }}</div>
    <div>{{ count }} {{ unit }}</div>
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
      <select v-model="velocitySuffix" @change="applyVelocity">
        <option value=""></option>
        <option v-for="[symbol] in SUFFIX_LIST" :key="symbol" :value="symbol">{{ symbol }}</option>
      </select>
      <div>{{ unit }}/s</div>
    </div>
    <div class="actions">
      <button @click="count = 0; velocity = 0">Reset</button>
    </div>
  </div>
</template>

<style>

.timed-counter {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
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

.velocity-input select {
  font-size: 1.1rem;
  padding: 0.4rem 0.75rem;
  border: 2px solid #333;
  border-radius: 6px;
  outline: none;
  background: white;
  cursor: pointer;
}

</style>
