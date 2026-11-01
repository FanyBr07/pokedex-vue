<script setup>
import { ref, onMounted } from 'vue'
import PokemonCard from './components/PokemonCard.vue'

const TOTAL_CARTAS = 40
const MAX_ID = 1025 // cantidad aproximada de Pokémon en la PokeAPI

const cartas = ref([])
const cargando = ref(true)
const error = ref('')

// 40 IDs distintos al azar
function idsAleatorios() {
  const ids = new Set()
  while (ids.size < TOTAL_CARTAS) {
    ids.add(Math.floor(Math.random() * MAX_ID) + 1)
  }
  return [...ids]
}

// Elige 4 movimientos al azar y limpia el nombre ("fire-punch" -> "fire punch")
function cuatroHabilidades(movimientos) {
  return [...movimientos]
    .sort(() => Math.random() - 0.5)
    .slice(0, 4)
    .map(m => m.move.name.replace('-', ' '))
}

const stat = (data, nombre) =>
  data.stats.find(s => s.stat.name === nombre).base_stat

async function barajar() {
  cargando.value = true
  error.value = ''
  try {
    const respuestas = await Promise.all(
      idsAleatorios().map(id =>
        fetch(`https://pokeapi.co/api/v2/pokemon/${id}`).then(r => r.json())
      )
    )
    cartas.value = respuestas.map(d => ({
      id: d.id,
      nombre: d.name,
      imagen: d.sprites.other['official-artwork'].front_default ?? d.sprites.front_default,
      tipos: d.types.map(t => t.type.name),
      habilidades: cuatroHabilidades(d.moves),
      hp: stat(d, 'hp'),
      ataque: stat(d, 'attack'),
      defensa: stat(d, 'defense'),
      velocidad: stat(d, 'speed'),
      altura: d.height / 10, // decímetros -> metros
      peso: d.weight / 10    // hectogramos -> kg
    }))
  } catch (e) {
    error.value = 'No se pudieron cargar las cartas. Revisa tu conexión e inténtalo de nuevo.'
  } finally {
    cargando.value = false
  }
}

onMounted(barajar)
</script>

<template>
  <main class="container py-4">
    <div class="d-flex justify-content-between align-items-center mb-4">
      <h1 class="h3 mb-0">Mazo de 40 cartas Pokémon</h1>
      <button class="btn btn-danger" :disabled="cargando" @click="barajar">
        Barajar de nuevo
      </button>
    </div>

    <p v-if="cargando">Cargando cartas…</p>
    <p v-else-if="error" class="text-danger">{{ error }}</p>

    <div v-else class="row row-cols-1 row-cols-sm-2 row-cols-lg-4 g-4">
      <div v-for="carta in cartas" :key="carta.id" class="col">
        <pokemon-card :pokemon="carta" />
      </div>
    </div>
  </main>
</template>