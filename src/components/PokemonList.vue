<template>
  <div id="imgContainer">
    <img src="@/assets/pokedex.png" width="700" height="300" />
  </div>
  <div id="Generation">
    <h1>Select Generation:</h1>
    <button @click="getPokemonList(1)">I</button>
    <button @click="getPokemonList(2)">II</button>
    <button @click="getPokemonList(3)">III</button>
    <button @click="getPokemonList(4)">IV</button>
    <button @click="getPokemonList(5)">V</button>
    <button @click="getPokemonList(6)">VI</button>
    <button @click="getPokemonList(7)">VII</button>
    <button @click="getPokemonList(8)">VIII</button>
  </div>
  <body>
    <div v-if="loadingCards" class="loader"></div>
    <!-- Pokemon Cards -->
    <div v-else>
    <ul>
      <div class="outerWrapper">
        <li v-for="(pokemon, index) in pokemonList" :key="index">
          <img
            :src="pokemon.sprites.other['official-artwork']['front_default']"
            width="250"
            height="250"
          />
          <h2 style="text-transform: capitalize">{{ pokemon.name }}</h2>
          <button id="button" @click="getDetails(pokemon.name)">
            Show Details
          </button>
        </li>
      </div>
    </ul>
    </div>
    <!-- modal -->
    <div :class="{ outerModal: showModal }">
      <div v-if="showModal" id="modal">
          <div v-if="loadingDetails" class="loader"></div>
          <div v-else>
        <div class="modal-header">
          <h3 style="color: white">Pokemon Species #{{ speciesNo }}</h3>
          <span class="close" @click="reset()">&times;</span>
        </div>
        <div id="detailsNav">
          <button @click="getDetails(name)" :style="detailsCheck ? 'background-color: #444141fd' : 'background-color: none'">Details</button>
          <button @click="getStats()" :style="statsCheck ? 'background-color: #444141fd' : 'background-color: none'">Stats</button>
          <button @click="getEvolution()" :style="evolutionCheck ? 'background-color: #444141fd' : 'background-color: none'">Evolution</button>
        </div>
        <div class="modalContent-Details" v-if="detailsCheck">
          <div :style="{ 'background-color': color }" id="detailsBackground">
            <img :src="sprite" width="150" height="150" id="sprite" />
          </div>
          <h1 class="details-name">{{ name }}</h1>
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
        <!-- Card Details  -->
        <div v-if="statsCheck">
          <div :style="{ 'background-color': color }" id="detailsBackground">
            <img :src="sprite" width="150" height="150" id="sprite" />
          </div>
          <h1 class="details-name">{{ name }}</h1>
          <div
            v-for="(pokemonStats, index) in stats"
            :key="index"
            id="outerStatsContainer"
          >
            <div id="progress">
              <div
                id="progressBar"
                :style="
                  pokemonStats.base_stat > 100
                    ? { width: '100%', 'background-color': color }
                    : {
                        width: pokemonStats.base_stat + '%',
                        'background-color': color,
                      }
                "
              >
                <p id="statNumber">{{ pokemonStats.base_stat }}</p>
              </div>
            </div>
            <p style="color: white">{{ pokemonStats.stat.name }}</p>
          </div>
        </div>
        <!-- Stats  -->
        <div v-if="evolutionCheck">
          <div
            id="firstEvolution"
            @click="getDetails(evolutionBegin)"
            style="cursor: pointer"
          >
            <h1 style="color: white; text-transform: capitalize">
              {{ evolutionBegin }}
              <img :src="firstEvolutionPicture" width="100" height="100" />
            </h1>
          </div>
          <div v-if="typeof evolutionSecond === 'string'">
            <img src="@/assets/down-arrow.png" width="50" height="50" />
            <div @click="getDetails(evolutionSecond)" style="cursor: pointer">
              <h1 style="color: white; text-transform: capitalize">
                {{ evolutionSecond }}
                <img :src="secondEvolutionPicture" width="100" height="100" />
              </h1>
            </div>
          </div>
          <div v-if="typeof evolutionThird === 'string'">
            <img src="@/assets/down-arrow.png" width="50" height="50" />
            <div @click="getDetails(evolutionThird)" style="cursor: pointer">
              <h1 style="color: white; text-transform: capitalize">
                {{ evolutionThird }}
                <img :src="thirdEvolutionPicture" width="100" height="100" />
              </h1>
            </div>
          </div>
        </div>
        <!-- Evolution -->
        </div>
      </div>
      <!-- Modal -->
    </div>
    <!-- Outer Modal experience  -->
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
      pokemonNumber: "",
      detailsCheck: false,
      stats: [],
      statsCheck: false,
      evolutionCheck: false,
      evolutionChainUrl: null,
      evolutionBegin: null,
      evolutionSecond: null,
      evolutionThird: null,
      firstEvolutionPicture: null,
      secondEvolutionPicture: null,
      thirdEvolutionPicture: null,
      loadingDetails: false,
      loadingCards: false
    };
  },

  methods: {
    reset() {
      (this.showModal = false),
        (this.name = null),
        (this.abilities = []),
        (this.speciesNo = null),
        (this.sprite = null),
        (this.height = null),
        (this.weight = null),
        (this.flavorText = null),
        (this.types = []),
        (this.color = null),
        (this.pokemonNumber = ""),
        (this.detailsCheck = false),
        (this.stats = []),
        (this.statsCheck = false),
        (this.evolutionCheck = false),
        (this.evolutionChainUrl = null),
        (this.evolutionBegin = null),
        (this.evolutionSecond = null),
        (this.evolutionThird = null),
        (this.firstEvolutionPicture = null),
        (this.secondEvolutionPicture = null),
        (this.thirdEvolutionPicture = null);
    },

    getPokemonList(generation) {
      this.loadingCards = true;
      this.pokemonList = [];
      this.promiseArray = [];
      let start = 0;
      let end = 0;
      switch (generation) {
        case 1:
          start = 1;
          end = 151;
          break;
        case 2:
          start = 152;
          end = 251;
          break;
        case 3:
          start = 252;
          end = 386;
          break;
        case 4:
          start = 387;
          end = 493;
          break;
        case 5:
          start = 494;
          end = 649;
          break;
        case 6:
          start = 650;
          end = 721;
          break;
        case 7:
          start = 722;
          end = 809;
          break;
        case 8:
          start = 810;
          end = 898;
          break;
        default:
          console.log("An error has occurred");
      }
      for (let i = start; i <= end; i++) {
        const pokeUrl = "https://pokeapi.co/api/v2/pokemon/" + i.toString();
        this.promiseArray.push(axios.get(pokeUrl));
      }

      Promise.all(this.promiseArray)
        .then((res) => {
          this.loadingCards = false;
          res.map((res) => this.pokemonList.push(res.data));
        })
        .catch((err) => console.log(err));
    },

    async getDetails(name) {
      this.loadingDetails = true;
      this.showModal = true;
      this.abilities = [];
      this.types = [];
      this.detailsCheck = true;
      this.statsCheck = false;
      this.evolutionCheck = false;
      await axios
        .get("https://pokeapi.co/api/v2/pokemon/" + name)
        .then((res) => {
          this.name = res.data.name;
          this.abilities = res.data.abilities;
          this.speciesNo = res.data.id;
          this.weight = res.data.weight;
          this.height = res.data.height;
          this.sprite = res.data.sprites.other.dream_world.front_default;
          this.types = res.data.types;
          this.pokemonNumber = res.data.id;
          this.stats = res.data.stats;
        })
        .catch((error) => {
          console.log(error);
        });
      await axios
        .get("https://pokeapi.co/api/v2/pokemon-species/" + name)
        .then((res) => {
          this.loadingDetails = false;
          this.flavorText = res.data.flavor_text_entries[1].flavor_text;
          this.color = res.data.color.name;
          this.evolutionChainUrl = res.data.evolution_chain.url;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    getStats() {
      this.evolutionCheck = false;
      this.detailsCheck = false;
      this.statsCheck = true;
    },

    async getEvolution() {
       this.loadingDetails = true;
      this.evolutionCheck = true;
      this.detailsCheck = false;
      this.statsCheck = false;
      this.evolutionBegin = null;
      this.evolutionSecond = null;
      this.evolutionThird = null;
      this.firstEvolutionPicture = null;

      await axios
        .get(this.evolutionChainUrl)
        .then((res) => {
          this.evolutionBegin = res.data.chain.species.name;
          axios
            .get("https://pokeapi.co/api/v2/pokemon/" + this.evolutionBegin)
            .then((res) => {
               this.loadingDetails = false;
              this.firstEvolutionPicture =
                res.data.sprites.other.dream_world.front_default;
            });
          this.evolutionSecond = res.data.chain.evolves_to[0].species.name;
          axios
            .get("https://pokeapi.co/api/v2/pokemon/" + this.evolutionSecond)
            .then((res) => {
              this.loadingDetails = false;
              this.secondEvolutionPicture =
                res.data.sprites.other.dream_world.front_default;
            });
          this.evolutionThird =
            res.data.chain.evolves_to[0].evolves_to[0].species.name;
          axios
            .get("https://pokeapi.co/api/v2/pokemon/" + this.evolutionThird)
            .then((res) => {
              this.loadingDetails = false;
              this.thirdEvolutionPicture =
                res.data.sprites.other.dream_world.front_default;
            });
        })
        .catch((error) => {
          console.log(error);
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
    this.getPokemonList(1);
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
  background-color: #c7c6c6fd;
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
  width: 500px;
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
  background-color: rgb(0, 0, 0);
  background-color: rgba(0, 0, 0, 0.4);
  width: 100%;
  height: 100%;
}

/* .modalContentDetails {
  position: relative;
  margin: auto;
  padding: 0;
  width: 80%;
} */

.details-name {
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
  from {
    -webkit-transform: scale(1);
  }
  to {
    -webkit-transform: scale(2);
  }
}
@keyframes zoom {
  from {
    transform: scale(0.4);
  }
  to {
    transform: scale(1);
  }
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
  margin-left: 60px;
}

.modal-body {
  padding: 2px 16px;
}

#detailsNav {
  margin-bottom: 60px;
  margin-left: 50px;
}

#detailsNav button {
  background-color: rgb(44, 37, 37);
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  font-size: 16px;
  cursor: pointer;
  float: left;
}

#detailsNav button:hover {
  background-color: #444141fd;
}

#progress {
  width: 60%;
  background-color: rgb(177, 177, 177);
  margin-bottom: 20px;
}

#progressBar {
  width: 1%;
  height: 20px;
  padding-bottom: 30px;
}

#statNumber {
  color: white;
  text-shadow: -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000,
    1px 1px 0 #000;
  margin-left: 40px;
}

#outerStatsContainer {
  display: flex;
  margin-left: 37px;
}
#outerStatsContainer p {
  margin-right: 20px;
  text-align: left;
  margin-left: 20px;
  text-transform: capitalize;
}

#Generation {
  margin-bottom: 50px;
  display: flex;
  margin-left: 20%;
}

#Generation h1 {
  margin-right: 30px;
}

#Generation button {
  border: none;
  color: black;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  font-size: 16px;
  cursor: pointer;
  float: left;
}

#Generation button:hover {
  background-color: #8a8888fd;
}

.loader {
  border: 16px solid #f3f3f3;
  border-top: 16px solid red;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  animation: spin 2s linear infinite;
  margin: auto;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
