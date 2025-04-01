<script setup lang="ts">
import { TYPE_COLORS } from '../constants/typeColors';

type PokemonType = keyof typeof TYPE_COLORS;

defineProps({
  id: Number,
  name: String,
  type: String as () => PokemonType,
  image: String,
});
</script>

<template>
  <div class="pokemon-card">
    <img :src="image" :alt="name" class="pokemon-image" />
    <h2>#{{ id }} - {{ name }}</h2>
    <p class="type" :style="{ backgroundColor: type ? TYPE_COLORS[type] : 'transparent' }">Type: {{ type || 'Unknown' }}</p>
  </div>
</template>