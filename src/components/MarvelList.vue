<template>
  <section>
    <h2>Liste des personnages Marvel</h2>
    <!-- Si lors du chargement avec Axios, on a une erreur, un message s'affiche, sinon on charge et on affiche le resultat -->
    <p v-if="isOnError">Une erreur est survenue.</p>
    <p v-else-if="isLoading">Chargement...</p>
    <template v-else>
      <ul>

        <li v-for="(perso, index) in persos" :key="index">
          {{ perso.name }}     
        </li>

      </ul>
      <div>
        <button
          type="button"
          @click="getPrevPage"
          :disabled="isPrevPageDisabled"
        >
          &lt; Page précédente
        </button>
        Page {{ page }}
        <button
          type="button"
          v-on:click="getNextPage"
          :disabled="isNextPageDisabled"
        >
          Page suivante &gt;
        </button>
      </div>
    </template>
  </section>
</template>

<script>
import axios from "axios";
import CryptoJS from "crypto-js";

// Les parametres necessaires pour pouvoir s'authentifier et utiliser l'API Marvel
var time = new Date().getTime();
var PRIV_KEY = "42219c5990132a1dbe3231fec6518c829f12bc1c";
var PUB_KEY = "c2ae0097c0712f74fbbf2df6ad5e9e05";
var hash = CryptoJS.MD5(time+PRIV_KEY+PUB_KEY).toString();

const URL_DE_API = "http://gateway.marvel.com/v1/public/characters?ts="+time+"&apikey="+PUB_KEY+"&hash="+hash;
const ITEMS_PER_PAGE = 11;

export default {
  name: "MarvelList",
  data() {
    return {
      isOnError: false,
      isLoading: true,
      persos: [],
      page: 1,
      totalCount: 0
    };
  },
  //Permet d'empecher d'aller avant la page 1 + permet de segmenter le resultat en pages de 11 a chaque fois
  computed: {
    isPrevPageDisabled() {
      return this.page <= 1;
    },
    isNextPageDisabled() {
      const pagesCount = Math.ceil(this.totalCount / ITEMS_PER_PAGE);
      return this.page >= pagesCount;
    },
  },
  created() {
    this.getPersos();
  },
  watch: {
    page(newVal, oldVal) {
      this.getPersos();
    },
  },
  methods: {
    getPersos() {
      this.isLoading = true;
      axios
        .get(URL_DE_API, {
          params: {
            limit: ITEMS_PER_PAGE,
            offset: ITEMS_PER_PAGE * (this.page - 1),
          },
        })
        .then((response) => {
          this.persos = response.data.data.results;
          this.totalCount = response.data.count;
        })
        .catch((error) => {
          this.isOnError = true;
        })
        .finally(() => {
          this.isLoading = false;
        });
    },
    getPrevPage() {
      this.page = this.page - 1;
    },
    getNextPage() {
      this.page = this.page + 1;
    },
  },
};
</script>