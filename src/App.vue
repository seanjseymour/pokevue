<template>
<pokemon-cards
  :pokemons="pokemons"
  @chosen="fetchEvolutions"
  :selectedId="selectedId"
/>
<pokemon-cards
  :pokemons="evolutions"
/>
</template>

<script>
import Card from "./components/Card.vue";
import PokemonCards from "./components/PokemonCards.vue";

const api = "https://pokeapi.co/api/v2/pokemon";
const min = 1;
const max = 800;

export default {
  components: {
    Card,
    PokemonCards,
  },

  data() {
    return {
      pokemons: [],
      evolutions: [],
      selectedId: null,
    };
  },

  async created() {
    const ids = [this.getRandomInt(min, max), this.getRandomInt(min, max), this.getRandomInt(min, max)]
    this.pokemons = await this.fetchData(ids);
  },

  mounted() {},

  methods: {
    async fetchEvolutions(pokemon) {
      this.evolutions = await this.fetchData(
        [pokemon.id + 1, pokemon.id + 2]
      )
      this.selectedId = pokemon.id
    },
    async fetchData(ids) {
      const responses = await Promise.all(
        ids.map((id) => window.fetch(`${api}/${id}`))
      );
      const json = await Promise.all(responses.map((data) => data.json()));

      return json.map((data) => ({
        id: data.id,
        name: data.name,
        sprite: data.sprites.other["official-artwork"].front_default,
        types: data.types.map((type) => type.type.name),
      }));
    },
    getRandomInt(min, max) {
      min = Math.ceil(min)
      max = Math.floor(max)
      return Math.floor(Math.random() * (max - min) + min)
    },
  },
};
</script>

<style scoped>
</style>
