<script setup lang="ts">
import { ref } from "vue";
import { watchDebounced } from "@vueuse/core";
import { v4 as uuidv4 } from "uuid";
import { compareAsc } from "date-fns";
import Form from "./components/Form.vue";
import GameList from "./components/GameList.vue";

enum GameState {
  FOR_SALE = "FOR_SALE",
  READY_TO_PLAY = "READY_TO_PLAY",
}

export interface Games {
  id: string;
  title: string;
  releaseDate: Date;
  genre: string;
  price?: number | string;
  discountPercentage?: number;
  images?: {
    url: string;
    previewUrl: string;
    alt: string;
  };
  state?: GameState;
  wishlist?: boolean;
}

const games = ref<Games[] | null>([
  {
    id: uuidv4(),
    title: "League of Legends",
    releaseDate: new Date(2009, 9, 27),
    genre: "Battle arena",
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: uuidv4(),
    title: "DOTA",
    releaseDate: new Date(2013, 6, 9),
    genre: "Strategy",
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: uuidv4(),
    title: "The Sims 4",
    releaseDate: new Date(2014, 8, 2),
    genre: "Simulation",
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: uuidv4(),
    title: "Inside",
    releaseDate: new Date(2019, 7, 29),
    genre: "Platform, Puzzle, Adventure",
    price: 100,
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: uuidv4(),
    title: "Limbo",
    releaseDate: new Date(2010, 8, 21),
    genre: "Platform, Puzzle, Adventure",
    price: 80,
    state: GameState.FOR_SALE,
    wishlist: false,
  },
]);

const filteredGames = ref<Games[] | undefined | null>(games.value);
const filterGames = () => {
  filteredGames.value = games.value?.filter((game) => {
    return game.title
      .toLowerCase()
      .includes(searchInputText.value.toLowerCase());
  });
};

const addToWishlist = (id: string) => {
  if (filteredGames.value) {
    const updatedGames = filteredGames.value.map((item) => {
      if (item.id === id) {
        item.wishlist = true;
      }
      return item;
    });
    games.value = updatedGames;
  }
};

const buy = (id: string) => {
  if (filteredGames.value) {
    const updatedGames = filteredGames.value.map((item) => {
      if (item.id === id) {
        item.state = GameState.READY_TO_PLAY;
        item.wishlist = false;
      }
      return item;
    });
    games.value = updatedGames;
  }
};

const searchInputText = ref("");

watchDebounced([searchInputText, games], filterGames, {
  debounce: 500,
  maxWait: 1000,
});

const remove = (id: string) => {
  if (games.value) {
    games.value = games.value.filter((game) => {
      return game.id !== id;
    });
  }
};

const sortDates = () => {
  if (filteredGames.value) {
    filteredGames.value.sort((a, b) =>
      compareAsc(a.releaseDate, b.releaseDate)
    );
  }
};

const add = (game: Games) => {
  try {
    if (game && games.value) {
      games.value = [
        ...games.value,
        {
          id: game.id,
          title: game.title,
          genre: game.genre,
          releaseDate: game.releaseDate,
          price: game.price,
          wishlist: false,
          state: GameState.FOR_SALE,
        },
      ];
    }
    console.log(game);
    console.log(games.value);
  } catch (err) {
    console.log(err);
  }
};
</script>

<template>
  <div className="min-h-screen bg-slate-800 flex flex-col pt-5">
    <h1 class="text-3xl self-center font-bold">Games</h1>
    <div class="flex justify-around py-10">
      <Form @add="add($event)"></Form>
      <GameList
        @remove="remove($event)"
        @buy="buy($event)"
        @addToWishlist="addToWishlist($event)"
        :games="games"
        :searchInputText="searchInputText"
        :filteredGames="filteredGames"
        :sortDates="sortDates"
      ></GameList>
    </div>
  </div>
</template>

<style scoped></style>
