<template>
  <div id="imgContainer">
    <img src="@/assets/pokedex.png" width="700" height="300" />
  </div>
  <body>
    <ul>
      <div class="outerWrapper">
        <li v-for="(pokemon, index) in pokemonList" :key="index">
          <img
            :src="pokemon.sprites.other['official-artwork']['front_default']"
            width="250"
            height="250"
          />
          <h2 style="text-transform:capitalize">{{ pokemon.name }}</h2>
          <button id="button" @click="getDetails(index + 1)">
            Show Details
          </button>
        </li>
      </div>

      <div v-if="showModal" id="modal">
        <h3>Pokemon species #{{ speciesNo }}</h3>
        <img :src="sprite" width="200" height="200" id="sprite" />
        <h1 id="details-name">{{name}}</h1>
        <h3>{{types}}</h3>
        <p style="color: white">{{ flavorText }}</p>
        <hr/>
        <div class="columns">
        <p id="details-height">Height: {{ getHeight() }}</p>
        <p id="details-weight">Weight: {{ getWeight() }}</p>
        </div>
        <button @click="showModal = false">Close</button>
      </div>
    </ul>
  </body>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      pokemonList: [],
      promiseArray: [],
      showModal: false,
      name: null,
      abilities: [],
      speciesNo: null,
      sprite: null,
      height: null,
      weight: null,
      flavorText: null,
      types: []
    };
  },

  methods: {
    getPokemonList() {
      for (let i = 1; i <= 151; i++) {
        const pokeUrl = "https://pokeapi.co/api/v2/pokemon/" + i.toString();
        this.promiseArray.push(axios.get(pokeUrl));
      }

      Promise.all(this.promiseArray)
        .then((res) => {
          res.map((res) => this.pokemonList.push(res.data));
        })
        .catch((err) => console.log(err));
    },

    getDetails(index) {
      this.showModal = true;
      this.abilities = [];
      this.types = [];
      axios.get("https://pokeapi.co/api/v2/pokemon/" + index).then((res) => {
        this.name = res.data.name;
        this.abilities.push(res.data.abilities);
        this.speciesNo = index;
        this.weight = res.data.weight;
        this.height = res.data.height;
        this.sprite = res.data.sprites.front_default;
        this.types.push(res.data.types.name)
      });
      axios
        .get("https://pokeapi.co/api/v2/pokemon-species/" + index)
        .then((res) => {
          this.flavorText = res.data.flavor_text_entries[1].flavor_text;
        });
    },

    getHeight() {
      let intHeight = parseFloat(this.height * 0.1).toFixed(1);
      return intHeight.toString() + "m";
    },

    getWeight() {
      let intWeightKg = parseFloat(this.weight * 0.1).toFixed(1);
      let intWeightLbs = (2.2046226218 * intWeightKg).toFixed(1);
     return intWeightKg.toString() + " kg " + "(" + intWeightLbs.toString() + " lbs" + ")";
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
  text-align: center;
  margin-right: 10px;
  margin-bottom: 10px;
  padding-bottom: 20px;
}

.outerWrapper :hover {
  background-color: rgb(218, 223, 236);
}

#button {
  background-color: rgb(170, 48, 48);
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  border-radius: 12px;
}
#modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 450px;
  margin-left: -150px;
  background-color: green;
  padding-top: 20px;
  padding-left: 40px;
  padding-right: 40px;
  text-align: center;
  border-radius: 30px;
}

#details-name {
  color: white;
  text-transform:capitalize;
  text-align: center;
}

/* #details-height, #details-weight{
  color: white;
  display: inline-block;
} */

.columns {
  columns: 40px 2;
  font-size: 20px;
  color: white;
  column-rule-style: solid;
  column-rule-color: grey;
  padding-bottom: 20px;
}



</style>
