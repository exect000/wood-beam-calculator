
<script setup>
import { reactive, computed } from 'vue';

const beam = reactive({
  length: 6,
  width: 0.1,
  height: 0.3,
  strength: 20000,
});

const isValid = computed(() =>
  Object.values(beam).every(
    (value) =>
      typeof value === 'number' &&
      Number.isFinite(value) &&
      value > 0
  )
);

const result = computed(() => {
  if (!isValid.value) return null;

  const W = (beam.width * beam.height ** 2) / 6;
  const q = (8 * W * beam.strength) / beam.length ** 2;

  return { W, q };
});
</script>

<template>
  <main class="container">
    <header>
      <h1>Koka sijas nestspējas kalkulators</h1>
      <p>Ievadiet sijas parametrus, lai aprēķinātu pieļaujamo slodzi.</p>
    </header>

    <section class="panel">
      <h2>Sijas parametri</h2>

      <div class="input-grid">
        <label>
          Sijas garums (L), m
          <input
            v-model.number="beam.length"
            type="number"
            min="0"
            step="any"
          />
        </label>

        <label>
          Sijas platums (B), m
          <input
            v-model.number="beam.width"
            type="number"
            min="0"
            step="any"
          />
        </label>

        <label>
          Sijas augstums (H), m
          <input
            v-model.number="beam.height"
            type="number"
            min="0"
            step="any"
          />
        </label>

        <label>
          Stiprība (f), kPa
          <input
            v-model.number="beam.strength"
            type="number"
            min="0"
            step="any"
          />
        </label>
      </div>
    </section>

    <section class="panel">
      <h2>Aprēķina rezultāti</h2>

      <div v-if="result" class="results">
        <div class="result-item">
          <span>Pretestības moments (W)</span>
          <strong>{{ result.W.toFixed(6) }} m³</strong>
        </div>

        <div class="result-item">
          <span>Pieļaujamā slodze (q)</span>
          <strong class="load">
            {{ result.q.toFixed(2) }} kN/m
          </strong>
        </div>
      </div>

      <p v-else class="error">
        Lūdzu, ievadiet pozitīvas skaitliskās vērtības.
      </p>
    </section>
  </main>
</template>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f3f5f9;
  color: #1e293b;
}

.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 28px;
}

header {
  text-align: center;
  margin-bottom: 24px;
}

h1 {
  font-size: 25px;
  margin-bottom: 10px;
}

header p {
  color: #64748b;
  font-size: 14px;
}

.panel {
  padding: 24px;
  margin-bottom: 20px;
  background: white;
  border-radius: 12px;
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.06);
}

h2 {
  margin: 0 0 20px;
  font-size: 18px;
}

.input-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 18px;
}

label {
  display: flex;
  flex-direction: column;
  gap: 8px;
  font-size: 14px;
  font-weight: 500;
}

input {
  width: 100%;
  padding: 11px;
  border: 1px solid #cbd5e1;
  border-radius: 6px;
  font-size: 15px;
}

input:focus {
  outline: none;
  border-color: #2563eb;
}

.results {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.result-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  padding: 15px;
  background: #f8fafc;
  border-radius: 8px;
}

.result-item span {
  color: #64748b;
  font-size: 14px;
}

.result-item strong {
  font-size: 17px;
}

.load {
  color: #2563eb;
}

.error {
  color: #dc2626;
}

@media (max-width: 500px) {
  .input-grid {
    grid-template-columns: 1fr;
  }

  .result-item {
    align-items: flex-start;
    flex-direction: column;
  }
}
</style>
