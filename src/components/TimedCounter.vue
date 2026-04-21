<script setup>
import { Pencil, Check, X } from 'lucide-vue-next'
import { ref, computed, onMounted, onUnmounted } from 'vue'

const unit = "$";

const SUFFIX_LIST = [
  ['k', 1e3], ['M', 1e6], ['G', 1e9], ['T', 1e12],
  ['P', 1e15], ['E', 1e18], ['Z', 1e21], ['Y', 1e24], ['R', 1e27], ['Q', 1e30]
]

const name = ref('Compteur')
const count = ref(0)
const velocity = ref(0)
const velocitySuffix = ref('')

const editing = ref(false)
const draftName = ref(name.value)
const draftInput = ref(0)
const draftSuffix = ref('')

function openEdit() {
  draftInput.value = velocityInput()
  draftSuffix.value = velocitySuffix.value
  editing.value = true
}

function velocityInput() {
  const multiplier = SUFFIX_LIST.find(([s]) => s === velocitySuffix.value)?.[1] ?? 1
  return multiplier === 1 ? velocity.value : velocity.value / multiplier
}

function applyEdit() {
  name.value = draftName.value
  const multiplier = SUFFIX_LIST.find(([s]) => s === draftSuffix.value)?.[1] ?? 1
  velocity.value = draftInput.value * multiplier
  velocitySuffix.value = draftSuffix.value
  editing.value = false
}

function cancelEdit() {
  draftName.value = name.value
  draftInput.value = count.value
  draftSuffix.value = velocitySuffix.value
  editing.value = false
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
    <!-- Name -->
    <div class="name">
      <div v-if="!editing" class="name-display">
        <h1>{{ name }}</h1>
      </div>
      <div v-if="editing" class="name-edit">
        <input v-model="draftName" type="text" />
      </div>
    </div>
    
    <!-- Count -->
    <div class="count">{{ formattedCount }} {{ unit }}</div>
    <!-- <div>{{ count }} {{ unit }}</div> -->

    <!-- Velocity -->
    <div v-if="!editing" class="velocity-display">
      <span class="velocity-label">Taux&nbsp;:</span>
      <span class="velocity-value">{{ velocityInput() }}{{ velocitySuffix }} {{ unit }}/s</span>
    </div>
    
    <div v-if="editing" class="velocity-input">
      <label for="velocity-field">Taux&nbsp;:</label>
      <input
        id="velocity-field"
        v-model="draftInput"
        type="number"
        min="0"
        step="1"
        autofocus
      />
      <select v-model="draftSuffix">
        <option value=""></option>
        <option v-for="[symbol] in SUFFIX_LIST" :key="symbol" :value="symbol">{{ symbol }}</option>
      </select>
      <span>{{ unit }}/s</span>
    </div>
    <div class="actions">
      <button v-if="!editing" @click="count = 0; velocity = 0">Reset</button>
      <button v-if="!editing" @click="openEdit"><Pencil :size="18" /></button>
      <button v-if="editing" @click="applyEdit"><Check :size="18" /></button>
      <button v-if="editing" @click="cancelEdit"><X :size="18" /></button>
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

.velocity-display {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 0.5rem;
}

.velocity-label {
  font-size: 0.875rem;
  color: #555;
}

.velocity-value {
  font-size: 1.1rem;
  font-weight: 600;
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

.name-edit input {
  font-size: 1.25rem;
  padding: 0.4rem 0.75rem;
  width: 100%;
  text-align: center;
  border: 2px solid #333;
  border-radius: 6px;
  outline: none;
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
