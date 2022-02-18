<template>
  <div id="imgContainer">
    <img src="@/assets/pokedex.png" width="700" height="300" />
  </div>
  <div>
    <ul>
      <div @click="showModal = true" class="outerWrapper">
        <li v-for="(pokemon, index) in pokemonList" :key="index">
          <img
            :src="pokemon.sprites.other['official-artwork']['front_default']"
            width="250"
            height="250"
          />
          <h2>{{ pokemon.name }}</h2>
          <h5>Pokemon species no. {{ index + 1 }}</h5>
        </li>
      </div>
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
      showModal: false,
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

    test() {
      console.log("Hi");
    },
  },

  mounted() {
    this.getPokemonList();
  },
};
</script>

<style scoped>
#imgContainer {
  text-align: center;
  width: 100%;
}

ul {
 margin-left: 100px;
}

li {
  display: inline-block;
  padding: 4px 10px;
  background-color: #eee;
  border-color: blue;
  text-align: center;
  margin-right: 10px;
  margin-bottom: 10px;
}

.outerWrapper :hover{
  background-color:rgb(197, 202, 216);
  cursor: pointer;
}

</style>
