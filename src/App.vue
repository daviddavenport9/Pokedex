<template>
<pokemon-header></pokemon-header>
 
</template>

<script>

import PokemonHeader from './components/PokemonHeader.vue'

export default {
  name: 'App',
  components: {
    PokemonHeader
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
