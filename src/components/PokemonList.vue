<template>
  <div id="imgContainer">
    <img src="@/assets/pokedex.png" width="700" height="300" />
  </div>
  <div>
    <ul class="list-group"> 
      <li class="list-group-item" v-for="pokemon in info.results" :key="pokemon.key">
        <p>{{ pokemon.name }}</p>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      info: {}
    };
  },

    created() {
      axios
        .get("https://pokeapi.co/api/v2/pokemon/")
        .then(({ data }) => (this.info = data));
    },
};
</script>

<style scoped>
img {
  display: inline-block;
}

#imgContainer {
  text-align: center;
  width: 100%;
}

li {
    display: inline-block;
}
</style>
