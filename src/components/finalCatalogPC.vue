<template>
  <div class="finalCatalogPC catalog" @mousemove="move">
    <finalHeader />
    <div class="catalog__title d-flex justify-center align-start flex-column">
      <h1 class="catalog__title-text">Games for PC</h1>
      <span class="catalog__title-subtitle mb-3">
        PC games, or personal computer games, started with the video game crash
        of 1983. PC games became popular after the development of the
        microprocessor and microcomputer. Some of the first PC games were Bertie
        the Brain, OXO and Spacewar! As the 3D graphics accelerators became
        faster and CPU power improved, PC games became more realistic and more
        accessible to produce. The PC market sales rocketed in the 80s when IBM
        computers and sound cards were generated. The platform involves
        different peripherals, gaming hardware, and software. These are mouse
        and keyboard; gamepads and motion controllers aren't obligatory, but
        still popularly accepted. Better hardware improves the game's accuracy;
        it usually lets the players use more NPCs than equivalents on other
        platforms. With the platform, the players can perform every sort of
        game. For example, shooters are easy to play due to the mouse
        controllers. However, the main reason for the PC games popularity is
        their lower prices and the backward compatibility with older titles,
        which leaves much to be desired on cosoles.</span
      >
      <div class="d-flex">
        <div>
          <b-form-select
            v-model="selectedFilter"
            :options="filterBy"
            :plain="true"
          ></b-form-select>
        </div>
        <div>
          <b-form-select
            v-model="selectReleaseDate"
            :options="releaseDate"
            :plain="true"
          ></b-form-select>
        </div>
      </div>
    </div>
    <div class="catalog__container">
      <finalCatalogItem
        v-for="(game, gameIndex) in PLATFORMSPC.results"
        :key="game.id"
        :product_data="game"
        @addToFavorite="addToFavorite"
        @deleteFromFavorite="deleteFromFavorite(gameIndex)"
      />
    </div>
    <div class="overflow-auto catalog__pagination d-flex justify-center">
      <b-pagination
        v-model="currentPage"
        :total-rows="PLATFORMSPC.count / perPage"
        :per-page="perPage"
        first-text="First"
        prev-text="Prev"
        next-text="Next"
        last-text="Last"
      ></b-pagination>
    </div>
    <finalFooter />
  </div>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
import finalCatalogItem from "./finalCatalogItem";
import finalHeader from "../components/finalHeader";
import finalFooter from "../components/finalFooter.vue";
import Vue from "vue";
import { eventBus } from "../main";

export default {
  name: "finalCatalogPc",
  components: {
    finalCatalogItem,
    finalHeader,
    finalFooter,
  },
  data() {
    return {
      perPage: 20,
      currentPage: 1,
      selectedFilter: "",
      selectReleaseDate: "",

      filterBy: [
        { value: "", text: "Order By:" },
        { value: "&ordering=-rating", text: "Rating" },
        { value: "&ordering=-metacritic", text: "Metacritic" },
        { value: "&ordering=-name", text: "Name" },
        { value: "&ordering=-popularity", text: "Popularity" },
        { value: "&ordering=-released", text: "Date Added" },
      ],
      releaseDate: [
        { value: "", text: "Release Date" },
        { value: "&dates=2020-01-01,2022-12-31", text: "2020-2022" },
        { value: "&dates=2010-09-01,2019-12-31", text: "2010-2019" },
        { value: "&dates=2000-09-01,2009-12-31", text: "2000-2009" },
        { value: "&dates=1990-09-01,1999-12-31", text: "1990-1999" },
        { value: "&dates=1980-09-01,1989-12-31", text: "1980-1989" },
        { value: "&dates=1970-09-01,1979-12-31", text: "1970-1979" },
        { value: "&dates=1960-09-01,1969-12-31", text: "1960-1969" },
        { value: "&dates=1950-09-01,1959-12-31", text: "1950-1959" },
      ],
    };
  },
  props: {
    filterRequest: {
      type: Array,
      required: false,
      default: () => ["", "&platforms=4", ""],
    },
  },
  watch: {
    currentPage(next) {
      this.$store.dispatch("getPlatformsPcByPage", [
        next,
        this.filterRequest.join(""),
      ]);
      window.scrollTo(0, 0);
    },
    selectedFilter() {
      Vue.set(this.filterRequest, 2, this.selectedFilter);
    },
    selectReleaseDate() {
      Vue.set(this.filterRequest, 0, this.selectReleaseDate);
    },

    filterRequest: {
      handler(newValue) {
        this.$store.dispatch("getPlatformsPcFilter", newValue.join(""));
        this.currentPage = 1;
      },
      deep: true,
    },
  },
  computed: {
    ...mapGetters(["PLATFORMSPC"]),
  },
  methods: {
    ...mapActions([
      "GET_PLATFORMS_PC_FROM_API",
      "ADD_TO_FAVORITE",
      "DELETE_FROM_FAVORITE",
    ]),
    deleteFromFavorite(index) {
      this.DELETE_FROM_FAVORITE(index);
    },
    addToFavorite(data) {
      this.ADD_TO_FAVORITE(data);
    },
    move($event) {
      if ($event.pageX < 50) {
        eventBus.$emit("isOpenSideMenu", true);
      } else {
        eventBus.$emit("isOpenSideMenu", false);
      }
    },
  },
  mounted() {
    this.GET_PLATFORMS_PC_FROM_API().then((responce) => {
      console.log(responce);
    });
  },
};
</script>
