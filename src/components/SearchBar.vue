<template>
  <div class="search-bar">
    <input
      type="text"
      v-model="searchQuery"
      @input="onSearch"
      placeholder="Rechercher un Pokémon..."
    />
  </div>
</template>

<script setup lang="ts">
import { ref, defineEmits } from 'vue';

const searchQuery = ref('');
const emit = defineEmits(['search']);

const onSearch = () => {
  emit('search', searchQuery.value);  
};

</script>