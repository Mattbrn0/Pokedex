<script setup lang="ts">
defineProps<{
  pokemon: {
    id: number;
    name: string;
    type: string;
    typeColor: string;
    image: string;
    height?: number;
    weight?: number;
    stats?: Array<{base_stat: number, stat: {name: string}}>;
    abilities?: Array<{ability: {name: string}}>;
  };
  isOpen: boolean;
}>();

defineEmits(['close']);
</script>

<template>
  <div v-if="isOpen" class="modal-overlay" @click="$emit('close')">
    <div class="modal-content" @click.stop>
      <button class="close-button" @click="$emit('close')">&times;</button>
      <div class="pokemon-details">
        <img :src="pokemon.image" :alt="pokemon.name" class="pokemon-image">
        <h2>{{ pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1) }}</h2>
        <div class="type-badge" :style="{ backgroundColor: pokemon.typeColor }">
          {{ pokemon.type }}
        </div>
        <div class="stats">
          <div v-if="pokemon.height" class="stat">Taille: {{ pokemon.height/10 }}m</div>
          <div v-if="pokemon.weight" class="stat">Poids: {{ pokemon.weight/10 }}kg</div>
        </div>
        <div v-if="pokemon.stats" class="stats-grid">
          <div v-for="stat in pokemon.stats" :key="stat.stat.name" class="stat-item">
            <div class="stat-name">{{ stat.stat.name }}</div>
            <div class="stat-value">{{ stat.base_stat }}</div>
          </div>
        </div>
        <div v-if="pokemon.abilities" class="abilities">
          <h3>Capacités:</h3>
          <div v-for="ability in pokemon.abilities" :key="ability.ability.name">
            {{ ability.ability.name }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
