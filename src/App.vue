<script setup>
import TimedCounter from './components/TimedCounter.vue'
</script>

<template>
  <TimedCounter />
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

</style>
