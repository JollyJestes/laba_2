<template>
  <div id="hello">
    <header>
      <h1>Pokemon Search</h1>
      <span>{{ totalPokemon }}</span>
    </header>
    <input type="text" v-model="searchTerm" @input="filterPokemon" @focus="focusInput" ref="search">
    <button @click="togglePokemonList">{{ showPokemonList ? 'Hide Pokemon List' : 'Show Pokemon List' }}</button>
    <ul v-show="showPokemonList">
      <li v-for="pokemon in filteredPokemon" :key="pokemon.name" @click="clickPokemon(pokemon.name)">
        <img :src="pokemon.image" :alt="pokemon.name">
        {{ pokemon.name }}
        <button @click="deletePokemon(pokemon.name)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'ModalPokemon',

  data() {
    return {
      pokemonList: [],
      searchTerm: '',
      showPokemonList: false,
      totalPokemon: '',
    };
  },

  created() {
    // получаем список покемонов
    fetch('https://pokeapi.co/api/v2/pokemon/')
        .then(response => response.json())
        .then(data => {
          this.totalPokemon = `Total Pokemon: ${data.count}`;
          data.results.forEach(pokemon => {
            const pokemonName = pokemon.name;
            // получаем URL-адрес изображения покемона
            fetch(`https://pokeapi.co/api/v2/pokemon/${pokemonName}`)
                .then(response => response.json())
                .then(pokemonData => {
                  const pokemonImage = pokemonData.sprites.front_default;
                  const pokemonObj = { name: pokemonName, image: pokemonImage };
                  this.pokemonList.push(pokemonObj);
                });
          });
        });
  },

  computed: {
    filteredPokemon() {
      // фильтруем список покемонов в соответствии с введенным пользователем запросом
      if (this.searchTerm) {
        return this.pokemonList.filter(pokemon =>
            pokemon.name.toLowerCase().includes(this.searchTerm.toLowerCase())
        );
      } else {
        return this.pokemonList;
      }
    },
  },

  methods: {
    deletePokemon(name) {
      // удаляем из списка покемонов
      const index = this.pokemonList.findIndex(pokemon => pokemon.name === name);
      if (index !== -1) {
        this.pokemonList.splice(index, 1);
      }
    },

    filterPokemon() {
      // фильтруем список покемонов в соответствии с введенным пользователем запросом
      this.$refs.search.focus();
    },

    focusInput() {
      this.searchTerm = '';
      this.filterPokemon();
    },

    clickPokemon(name) {
      this.searchTerm = name;
      this.$refs.search.focus();
    },

    searchPokemon() {
      this.filterPokemon(this.searchTerm);
    },

    togglePokemonList() {
      this.showPokemonList = !this.showPokemonList;
    },
  },
};
</script>

<style scoped lang="less"></style>
