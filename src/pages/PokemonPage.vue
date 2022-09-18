<template>
  <b-container>
    <!-- //Mientras no haya Pokemon o no haya cargado se mostrara esto -->
    <h1 v-if="!pokemon">Espere por favor...</h1>

    <div v-else>
      <h1>Quien es este Pokemon?</h1>

      <!-- La imagen del Pokemon esta basado en una de las 4 opciones -->
      <PokemonPicture :pokemonId="pokemon.id" :showPokemon="showPokemon" />

      <!-- el selection viene del componente hijo, que llevara un  metodo y el evento que trae el @selection es opcional ponerselo x ser el 1er argumento -->
      <PokemonOptions
      v-show="activeOptions"
      :pokemons="pokemonArray"
      @selection="checkAnswer"
      />
      <h2 v-show="hiddenPoints">Combo: {{points}}x</h2>
      <!-- Ambos mensajes saldran al mismo tiempo cuando se haga click -->
      <!-- <h2 v-if="showTotal">Intentos restantes:{{ total }}</h2> -->
      <template v-if="showAnswer">
        <h2>{{ message }}</h2>
        <b-button
          variant="outline-primary"
          size="lg"
          :disabled="this.lives <= 0"
          @click="newGame"
        >
          Siguiente Pokemon
        </b-button>
        <hr />

        <b-button
          variant="success"
          size="lg"
          class="h1 mb-2"
          v-show="this.lives <= 0"
          @click="start"
          >Empezar de nuevo</b-button
        >
      </template>
    </div>
  </b-container>
</template>

<script>
import PokemonPicture from "@/components/PokemonPicture.vue";
import PokemonOptions from "@/components/PokemonOptions.vue";
import getPokemonOptions from "@/helpers/getPokemonOptions.js";

export default {
  components: { PokemonPicture, PokemonOptions },
  data() {
    return {
      pokemonArray: [],
      pokemon: null, //este es el pokemon que debe adivinar la persona, estara dentro del array que se muestre
      showPokemon: false,
      showAnswer: false,
      showTotal: false,
      message: "",
      lives: 3,
      points: 0,
      startAgain: false,
      activeOptions: true,
      hiddenPoints: false,
    };
  },
  methods: {
    async mixPokemonArray() {
      this.pokemonArray = await getPokemonOptions(); //getPokemonOptios trae consigo el id y el nombre del pokemon y toda la funcionalidad de mezclar el array
      const rndInt = Math.floor(Math.random() * 4); // Generate randoms numbers from 0 to 3
      this.pokemon = this.pokemonArray[rndInt]; //el [rndInt] mezclara la respuesta dentro del array de 4 opciones
      // solo ocuparemos llevarnos el pokemonArray al componente PokemonOptions para despues ciclar el array con la funcionalidad de este metodo
    },

    // Aqui traemos el argumento del evento, que es el pokemon.id
    checkAnswer(selectedId) {
      this.showPokemon = true;
      this.showAnswer = true;
      if (selectedId === this.pokemon.id) {
        this.points = this.points + 1;
        this.activeOptions = false; //menu apagado cuando responda correctamente
        this.hiddenPoints = true; 
        return (this.message = `Correcto, es ${this.pokemon.name}.`);
      } else {
        this.showTotal = true;
        this.lives = this.lives - 1;
        this.activeOptions = false; //menu apagado cuando responda incorrectamente
        return (this.message = `Oops, era ${this.pokemon.name}. Intentos restantes: ${this.lives} `);
      }
    },

    newGame() {
      this.activeOptions = true;
      this.show = true;
      this.showPokemon = false;
      this.showAnswer = false;
      this.showTotal = false;
      this.pokemonArray = [];
      this.pokemon = null;
      this.mixPokemonArray();
    },
    start() {
      this.activeOptions = true;
      this.lives = 3;
      this.points = 0;
      this.show = true;
      this.showPokemon = false;
      this.showAnswer = false;
      this.showTotal = false;
      this.pokemonArray = [];
      this.pokemon = null;
      this.mixPokemonArray();
    },
  },

  mounted() {
    this.mixPokemonArray();
  },
};
</script>
