<template>
  <div id="app">
    <ModalPokemon msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import ModalPokemon from './components/ModalPokemon.vue'

export default {
  components: {
    ModalPokemon
  },
  data() {
    return{
    pokemonList: [],
    searchTerm: '',
    showPokemonList: false,
  }
  },
  created: function() {
    //получаем список покемонов
    fetch('https://pokeapi.co/api/v2/pokemon/')
        .then(response => response.json())
        .then(data => {
          this.totalPokemon = `Total Pokemon: ${data.count}`;
          data.results.forEach(pokemon => {
            const pokemonName = pokemon.name;
            //получаем  URL-адрес изображения покемона
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
    filteredPokemon: function() {
      //фильтруем список покемонов в соответствии с введенным пользователем запросом
      if (this.searchTerm) {
        return this.pokemonList.filter(pokemon => pokemon.name.toLowerCase().includes(this.searchTerm.toLowerCase()));
      } else {
        return this.pokemonList;
      }
    },
  },
  methods: {
    deletePokemon: function(name) {
      //удаляем из списка покемонов
      const index = this.pokemonList.findIndex(pokemon => pokemon.name === name);
      if (index !== -1) {
        this.pokemonList.splice(index, 1);
      }
    },
    filterPokemon: function() {
      //фильтруем список покемонов в соответствии с введенным пользователем запросом
      this.$refs.search.focus();
    },
    focusInput: function() {
      this.searchTerm = '';
      this.filterPokemon();
    },
    clickPokemon: function(name) {

      this.searchTerm = name;
      this.$refs.search.focus();
    },
    searchPokemon: function() {
      this.filterPokemon(this.searchTerm);
    },

    togglePokemonList: function() {
      this.showPokemonList = !this.showPokemonList;
    },
  },
}
</script>

<style lang="less">
@import "assets/less/index.less";
</style>
