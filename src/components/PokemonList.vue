<template>
  <div id="imgContainer">
    <img src="@/assets/pokedex.png" width="700" height="300" />
  </div>
  <body>
    <!-- Pokemon Cards -->
    <ul>
      <div class="outerWrapper">
        <li v-for="(pokemon, index) in pokemonList" :key="index">
          <img
            :src="pokemon.sprites.other['official-artwork']['front_default']"
            width="250"
            height="250"
          />
          <h2 style="text-transform: capitalize">{{ pokemon.name }}</h2>
          <button id="button" @click="getDetails(index + 1)">
            Show Details
          </button>
        </li>
      </div>
    </ul>
    <!-- modal -->
    <div :class="{'outerModal': showModal}">
    <div v-if="showModal" id="modal" class="myModal">
      <div class="modal-header">
           <h3 style="color: white">Pokemon species #{{ speciesNo }}</h3>
        <span class="close" @click="showModal = false">&times;</span>
      </div>
      <div class="modalContent">
        <div :style="{ 'background-color': color }" id="detailsBackground">
          <img :src="sprite" width="150" height="150" id="sprite" />
        </div>
        <h1 id="details-name">{{ name }}</h1>
        <h3
          v-for="(pokemonTypes, index) in types"
          :key="index"
          class="pokemonTypes"
          :style="{ color: color, 'border-color': color }"
        >
          {{ pokemonTypes.type.name }}
        </h3>
        <p style="color: white">{{ flavorText }}</p>
        <hr />
        <div class="columns">
          <p>Height: {{ getHeight() }}</p>
          <p>Weight: {{ getWeight() }}</p>
        </div>
        <h3 style="color: white">Abilities:</h3>
        <h5
          v-for="(ability, index) in abilities"
          :key="index"
          class="pokemonAbilities"
          :style="{ 'background-color': color }"
        >
          {{ ability.ability.name }}
        </h5>
      </div>
    </div>
    </div>
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
      types: [],
      color: null,
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
        this.abilities = res.data.abilities;
        this.speciesNo = index;
        this.weight = res.data.weight;
        this.height = res.data.height;
        this.sprite = res.data.sprites.other.dream_world.front_default;
        this.types = res.data.types;
      });
      axios
        .get("https://pokeapi.co/api/v2/pokemon-species/" + index)
        .then((res) => {
          this.flavorText = res.data.flavor_text_entries[1].flavor_text;
          this.color = res.data.color.name;
        });
    },

    getHeight() {
      let intHeight = parseFloat(this.height * 0.1).toFixed(1);
      return intHeight.toString() + "m";
    },

    getWeight() {
      let intWeightKg = parseFloat(this.weight * 0.1).toFixed(1);
      let intWeightLbs = (2.2046226218 * intWeightKg).toFixed(1);
      return (
        intWeightKg.toString() +
        " kg " +
        "(" +
        intWeightLbs.toString() +
        " lbs" +
        ")"
      );
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
  top: 10%;
  left: 47%;
  width: 450px;
  margin-left: -150px;
  background-color: rgb(44, 37, 37);
  padding-top: 20px;
  padding-left: 40px;
  padding-right: 40px;
  text-align: center;
  border-radius: 30px;
  height: auto;
  -webkit-animation-name: zoom;
    -webkit-animation-duration: 0.4s;
    animation-name: zoom;
    animation-duration: 0.4s;
}

.outerModal {
  top: 0;
  left: 0;
  display: block;
  position: fixed;
  z-index: 1;
  padding-top: 100px;
   background-color: rgb(0,0,0);
   background-color: rgba(0,0,0,0.4);
   width: 100%;
  height: 100%;
}



.modalContent {
  position: relative;
  margin: auto;
  padding: 0;
  width: 80%;
}

#details-name {
  color: white;
  text-transform: capitalize;
  text-align: center;
}

.columns {
  columns: 40px 2;
  font-size: 20px;
  color: white;
  padding-bottom: 0px;
}

#closeButton {
  background-color: rgb(67, 1, 248);
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  border-radius: 12px;
  margin-bottom: 20px;
  margin-top: 30px;
}

.pokemonAbilities {
  display: inline-block;
  border: 1px solid black;
  box-shadow: 1px 2px 2px black;
  border-radius: 15px;
  padding: 3%;
  margin-right: 20px;
  margin-bottom: 20px;
  color: white;
  text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
    1px 1px 0 #000;
}
.pokemonTypes {
  display: inline-block;
  border: 1px solid;
  box-shadow: 1px 2px 2px black;
  border-radius: 15px;
  padding: 3%;
  margin-right: 20px;
  text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
    1px 1px 0 #000;
}

#detailsBackground {
  border-radius: 50%;
  width: 200px;
  height: 200px;
  margin: auto;
}

#sprite {
  text-align: center;
  margin: 20px;
}

@-webkit-keyframes zoom {
    from {-webkit-transform:scale(1)}
    to {-webkit-transform:scale(2)}
}
@keyframes zoom {
    from {transform:scale(0.4)}
    to {transform:scale(1)}
}

/* The Close Button */
.close {
  color: white;
  float: right;
  font-size: 28px;
  font-weight: bold;
  margin: -20px;
}

.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}

.modal-header {
  padding: 2px 16px;
  color: white;
  border-bottom: none;
}

.modal-body {
  padding: 2px 16px;
}
</style>
