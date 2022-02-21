<template>
<pokemon-header></pokemon-header>
 <pokemon-list></pokemon-list>
</template>

<script>

import PokemonHeader from './components/PokemonHeader.vue'
import PokemonList from './components/PokemonList.vue'

export default {
  name: 'app',
  components: {
    PokemonHeader,
    PokemonList
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Poppins&display=swap');


#app {
  font-family: 'Poppins', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
