<script setup>
import { ref, watch, onMounted, onUnmounted } from 'vue'
import { Plus, Pause, Play, TrendingUp } from 'lucide-vue-next'
import TimedCounter from './components/TimedCounter.vue'

const ROSTER_KEY = 'timed-counters-roster'

const savedRoster = (() => { try { return JSON.parse(localStorage.getItem(ROSTER_KEY)) } catch { return null } })()
const counters = ref(savedRoster?.counters ?? [0])
const counterRefs = ref([])
const paused = ref(savedRoster?.paused ?? false)
let nextId = savedRoster?.nextId ?? 1

watch([counters, paused], ([newCounters, newPaused]) => {
  localStorage.setItem(ROSTER_KEY, JSON.stringify({ counters: newCounters, nextId, paused: newPaused }))
}, { deep: true })

function pause() { paused.value = true }
function play() { paused.value = false }

function addCounter() {
  counters.value.push(nextId++)
  //localStorage.setItem(ROSTER_KEY, JSON.stringify({ counters: counters.value, nextId }))
}

function removeCounter(id) {
  localStorage.removeItem(`timed-counter-${id}`)
  counters.value = counters.value.filter(c => c !== id)
}

let timer
onMounted(() => { timer = setInterval(() => { if (!paused.value) counterRefs.value.forEach(c => c.tick()) }, 1000) })
onUnmounted(() => clearInterval(timer))
</script>

<template>
    <header class="header">
        <div class="header-left">
            <TrendingUp v-if="!paused" :size="36" />
            <Pause v-else :size="36" />
            <div class="app-name">Gains</div>
        </div>

        <div class="header-right">
            <button v-if="!paused" @click="pause"><Pause :size="22" /></button>
            <button v-else @click="play"><Play :size="22" /></button>
        </div>
    </header>
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

.header {
    width: 100vw;
    padding: .5rem;
    font-weight: 600;

    position: fixed;
    top: 0;
    left:0;
    right:0;

    background-color: #0008;
    color: #fff;

    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
}

.header-left {
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: .5rem;
}

main {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
  padding-left: .25rem;
  padding-right: .25rem;
  padding-top: 5rem;
  padding-bottom: 2rem;
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
  padding: 0.333rem .666rem;
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
