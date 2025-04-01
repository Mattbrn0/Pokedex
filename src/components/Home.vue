<script setup lang="ts">
import { ref, onMounted } from "vue";
import Pokemon from "./Pokemon.vue";
import SearchBar from "./SearchBar.vue";
import PokemonModal from "./PokemonModal.vue";
import FilterType from "./FilterType.vue";
import { TYPE_COLORS } from '../constants/typeColors';

type PokemonType = keyof typeof TYPE_COLORS;

interface Pokemon {
  id: number;
  name: string;
  type: PokemonType;
  typeColor: string;
  image: string;
  height?: number;
  weight?: number;
  stats?: Array<{base_stat: number, stat: {name: string}}>;
  abilities?: Array<{ability: {name: string}}>;
}

const pokemons = ref<Pokemon[]>([]);
const filteredPokemons = ref<Pokemon[]>([]);
const searchQuery = ref("");
const selectedType = ref('all');

const fetchPokemons = async () => {
  try {
    const response = await fetch("https://pokeapi.co/api/v2/pokemon?limit=100000&offset=0");
    const data = await response.json();

    const pokemonDetails = await Promise.all(
      data.results.map(async (pokemon: { url: RequestInfo | URL; }) => {
        const res = await fetch(pokemon.url);
        const details = await res.json();
        const type = details.types[0].type.name as PokemonType;
        return {
          id: details.id,
          name: details.name,
          type: type,
          typeColor: TYPE_COLORS[type],
          image: details.sprites.front_default,
        };
      })
    );

    pokemons.value = pokemonDetails;
    filteredPokemons.value = pokemonDetails;
  } catch (error) {
    console.error("Erreur lors du chargement des Pokémon:", error);
  }
};

const filterPokemons = (query: string) => {
  searchQuery.value = query.toLowerCase();
  applyFilters();
};

const handleTypeFilter = (type: PokemonType | 'all') => {
  selectedType.value = type;
  applyFilters();
};

const applyFilters = () => {
  filteredPokemons.value = pokemons.value.filter(pokemon => {
    const matchesSearch = pokemon.name.toLowerCase().includes(searchQuery.value);
    const matchesType = selectedType.value === 'all' || pokemon.type === selectedType.value;
    return matchesSearch && matchesType;
  });
};

const selectedPokemon = ref<Pokemon | null>(null);
const isModalOpen = ref(false);

const openPokemonDetails = async (pokemon: Pokemon) => {
  try {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.id}`);
    const details = await response.json();
    selectedPokemon.value = {
      ...pokemon,
      height: details.height,
      weight: details.weight,
      stats: details.stats,
      abilities: details.abilities,
    };
    isModalOpen.value = true;
  } catch (error) {
    console.error("Erreur lors du chargement des détails:", error);
  }
};

onMounted(fetchPokemons);
</script>

<template>
  <div>
    <SearchBar @search="filterPokemons" />
    <FilterType @filterType="handleTypeFilter" />
    <div class="pokemon-container">
      <Pokemon
        v-for="pokemon in filteredPokemons"
        :key="pokemon.id"
        :id="pokemon.id"
        :name="pokemon.name"
        :type="pokemon.type"
        :image="pokemon.image"
        @click="openPokemonDetails(pokemon)"
        class="clickable"
      />
      <div v-if="filteredPokemons.length === 0" class="no-results">
        Aucun Pokémon ne correspond à la recherche.
      </div>
    </div>
    <PokemonModal
      v-if="selectedPokemon"
      :pokemon="selectedPokemon"
      :isOpen="isModalOpen"
      @close="isModalOpen = false"
    />
  </div>
</template>

<style scoped>
.pokemon-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  padding: 20px;
}

.clickable {
  cursor: pointer;
  transition: transform 0.2s ease;
}

.clickable:hover {
  transform: scale(1.05);
}
</style>