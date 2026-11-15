
<script setup>
import { ref } from 'vue';

const count = ref(0);
</script>

<template>
  <main class="container">
    <h1>Koka sijas nestspējas kalkulators</h1>

    <p>
      Aplikācija izveidota, izmantojot Electron.js un Vue 3.
    </p>

    <div class="card">
      <h2>Vue 3 pārbaude</h2>

      <p>Pogas nospiešanas reizes: {{ count }}</p>

      <button @click="count++">
        Pārbaudīt
      </button>
    </div>
  </main>
</template>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background-color: #f4f6f9;
  color: #222;
}

.container {
  padding: 40px;
  text-align: center;
}

.card {
  margin: 30px auto;
  padding: 25px;
  max-width: 400px;
  border-radius: 12px;
  background-color: white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

button {
  padding: 10px 20px;
  background-color: #2563eb;
  border: none;
  border-radius: 6px;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #1d4ed8;
}
</style>
