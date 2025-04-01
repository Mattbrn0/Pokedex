<script setup lang="ts">
import { ref } from 'vue';
import { TYPE_COLORS } from '../constants/typeColors';

type PokemonType = keyof typeof TYPE_COLORS;

const selectedType = ref<PokemonType | 'all'>('all');
const types = Object.keys(TYPE_COLORS) as PokemonType[];

const emit = defineEmits<{
  (event: 'filterType', type: PokemonType | 'all'): void
}>();

const handleTypeChange = (type: PokemonType | 'all') => {
  selectedType.value = type;
  emit('filterType', type);
};
</script>

<template>
  <div class="type-filters">
    <button 
      class="type-button"
      :class="{ active: selectedType === 'all' }"
      @click="handleTypeChange('all')"
    >
      Tous
    </button>
    <button
      v-for="type in types"
      :key="type"
      class="type-button"
      :class="{ active: selectedType === type }"
      :style="{ backgroundColor: TYPE_COLORS[type] }"
      @click="handleTypeChange(type)"
    >
      {{ type }}
    </button>
  </div>
</template>

<style scoped>

</style>