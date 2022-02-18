<template>
  <div id="imgContainer">
    <img src="@/assets/pokedex.png" width="700" height="300" />
  </div>
  <div>
    <ul>
      
      <li v-for="(pokemon, index) in pokemonList" :key="index">
        <h2>{{ pokemon.name }}</h2>
        <img
          :src="pokemon.sprites.other['official-artwork']['front_default']"
          width="250"
          height="250"
        />
        <h5>Pokemon species no. {{ index }}</h5>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      pokemonList: [],
      PokemonURL: "https://pokeapi.co/api/v2/pokemon?limit=100",
      promiseArray: [],
      showModal: false
    };
  },

  methods: {
    getPokemonList() {
      for (let i = 1; i <= 102; i++) {
        const pokeUrl = "https://pokeapi.co/api/v2/pokemon/" + i.toString();
        this.promiseArray.push(axios.get(pokeUrl));
      }

      Promise.all(this.promiseArray)
        .then((res) => {
          res.map((res) => this.pokemonList.push(res.data));
        })
        .catch((err) => console.log(err));
    },
  },

  mounted() {
    this.getPokemonList();
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

ul {
  list-style: none;
  width: 100%;
}

li {
  display: inline-block;
  padding: 4px 10px;
  background-color: #eee;
  border-radius: 5px;
  text-align: center;
}
</style>
