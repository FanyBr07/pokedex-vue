<script setup>
import { computed } from 'vue'

const props = defineProps({
  pokemon: { type: Object, required: true }
})

// Color del marco según el primer tipo del Pokémon
const colores = {
  fire: '#f08030', water: '#6890f0', grass: '#78c850', electric: '#f8d030',
  psychic: '#f85888', ice: '#98d8d8', dragon: '#7038f8', dark: '#705848',
  fairy: '#ee99ac', normal: '#a8a878', fighting: '#c03028', flying: '#a890f0',
  poison: '#a040a0', ground: '#e0c068', rock: '#b8a038', bug: '#a8b820',
  ghost: '#705898', steel: '#b8b8d0'
}

const color = computed(() => colores[props.pokemon.tipos[0]] ?? '#a8a878')
</script>

<template>
  <article class="poke-card" :style="{ '--tipo': color }">
    <header class="d-flex justify-content-between align-items-baseline">
      <h2 class="h5 text-capitalize mb-0">{{ pokemon.nombre }}</h2>
      <span class="fw-bold">HP {{ pokemon.hp }}</span>
    </header>

    <div class="arte">
      <img :src="pokemon.imagen" :alt="`Arte de ${pokemon.nombre}`" loading="lazy">
    </div>

    <p class="datos">
      N.º {{ pokemon.id }} · {{ pokemon.altura }} m · {{ pokemon.peso }} kg
    </p>

    <div class="mb-2">
      <span
        v-for="tipo in pokemon.tipos"
        :key="tipo"
        class="badge text-capitalize me-1"
        :style="{ background: colores[tipo] ?? '#a8a878' }"
      >{{ tipo }}</span>
    </div>

    <ul class="list-unstyled habilidades">
      <li v-for="hab in pokemon.habilidades" :key="hab" class="text-capitalize">
        {{ hab }}
      </li>
    </ul>

    <footer class="stats">
      <span>Ataque {{ pokemon.ataque }}</span>
      <span>Defensa {{ pokemon.defensa }}</span>
      <span>Velocidad {{ pokemon.velocidad }}</span>
    </footer>
  </article>
</template>

<style scoped>
.poke-card {
  background: #f6e7a4;
  border: 10px solid var(--tipo);
  border-radius: 14px;
  padding: 10px 12px 12px;
  height: 100%;
  color: #222;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.35);
}
.arte {
  background: radial-gradient(circle at 50% 40%, #fff, var(--tipo));
  border: 3px solid #c9a93a;
  border-radius: 6px;
  text-align: center;
  margin: 8px 0 4px;
}
.arte img { width: 150px; height: 150px; object-fit: contain; }
.datos { font-size: 0.75rem; font-style: italic; text-align: center; margin: 0 0 8px; }
.habilidades li {
  font-size: 0.85rem;
  border-bottom: 1px solid rgba(0, 0, 0, 0.2);
  padding: 3px 0;
}
.stats {
  display: flex;
  justify-content: space-between;
  font-size: 0.7rem;
  margin-top: 8px;
}
</style>