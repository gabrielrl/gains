<script setup>
import { ref, watch, onMounted, onUnmounted } from 'vue'
import { Plus } from 'lucide-vue-next'
import TimedCounter from './components/TimedCounter.vue'

const ROSTER_KEY = 'timed-counters-roster'

const savedRoster = (() => { try { return JSON.parse(localStorage.getItem(ROSTER_KEY)) } catch { return null } })()
const counters = ref(savedRoster?.counters ?? [0])
const counterRefs = ref([])
let nextId = savedRoster?.nextId ?? 1

watch(counters, (val) => {
  localStorage.setItem(ROSTER_KEY, JSON.stringify({ counters: val, nextId }))
}, { deep: true })

function addCounter() {
  counters.value.push(nextId++)
  localStorage.setItem(ROSTER_KEY, JSON.stringify({ counters: counters.value, nextId }))
}

function removeCounter(id) {
  localStorage.removeItem(`timed-counter-${id}`)
  counters.value = counters.value.filter(c => c !== id)
}

let timer
onMounted(() => { timer = setInterval(() => { counterRefs.value.forEach(c => c.tick()) }, 1000) })
onUnmounted(() => clearInterval(timer))
</script>

<template>
    <main>
        <TimedCounter
          v-for="id in counters"
          :key="id"
          :id="id"
          ref="counterRefs"
          :removable="counters.length > 1"
          @remove="removeCounter(id)"
        />
        <button class="add-counter" @click="addCounter">
            <Plus :size="22" />
        </button>
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

.add-counter {
  margin-top: 1rem;
  font-size: 1.25rem;
  padding: 0.5rem 1.25rem;
  border: 2px dashed #333;
  border-radius: 6px;
  background: white;
  cursor: pointer;
  transition: background 0.15s;
  display: flex;
  align-items: center;
}

.add-counter:hover {
  background: #333;
  color: white;
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

/* Remove spinners from number inputs. */

/* Chrome, Safari, Edge */
input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type=number] {
  -moz-appearance: textfield;
}

</style>
