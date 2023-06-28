<script setup lang="ts">
import { ref } from "vue";
import { watchDebounced } from "@vueuse/core";
import { v4 as uuidv4 } from "uuid";
import { compareAsc } from "date-fns";

enum GameState {
  FOR_SALE = "FOR_SALE",
  READY_TO_PLAY = "READY_TO_PLAY",
}

interface Games {
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
const titleInput = ref("");
const genreInput = ref("");
const releaseDateInput = ref("");
const priceInput = ref("");

const filteredGames = ref<Games[] | undefined | null>(games.value);
const filterGames = () => {
  filteredGames.value = games.value?.filter((game) => {
    return game.title
      .toLowerCase()
      .includes(searchInputText.value.toLowerCase());
  });
};

watchDebounced([searchInputText, games], filterGames, {
  debounce: 500,
  maxWait: 1000,
});

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
      console.log(game);
    }
  } catch (err) {
    console.log(err);
  } finally {
    titleInput.value = "";
    genreInput.value = "";
    releaseDateInput.value = "";
    priceInput.value = "";
  }
};

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
</script>

<template>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css"
  />
  <div className="min-h-screen bg-slate-800 flex flex-col pt-5">
    <h1 class="text-3xl self-center font-bold">Games</h1>
    <div class="flex justify-around py-10">
      <form @submit.prevent className="flex flex-col gap-5">
        <h2 class="self-center text-xl font-bold">Add Game</h2>
        <fieldset class="flex flex-col gap-2">
          <label for="titleInput">Title *</label>
          <input
            v-model="titleInput"
            name="titleInput"
            type="text"
            class="input input-bordered w-full max-w-xs"
          />
        </fieldset>
        <fieldset class="flex flex-col gap-2">
          <label for="genreInput">Genre *</label>
          <input
            name="genreInput"
            v-model="genreInput"
            type="text"
            class="input input-bordered w-full max-w-xs"
          />
        </fieldset>
        <fieldset class="flex flex-col gap-2">
          <label for="releaseDateInput">Release Date *</label>
          <input
            v-model="releaseDateInput"
            name="releaseDateInput"
            type="date"
            class="input input-bordered w-full max-w-xs"
          />
        </fieldset>
        <fieldset class="flex flex-col gap-2">
          <label for="priceInput">Price</label>
          <input
            name="priceInput"
            v-model="priceInput"
            type="number"
            class="input input-bordered w-full max-w-xs"
          />
        </fieldset>
        <button
          type="submit"
          class="btn btn-xs sm:btn-sm md:btn-md lg:btn-lg mt-5"
          @click="
            if (titleInput && genreInput && releaseDateInput) {
              add({
                id: uuidv4(),
                title: titleInput,
                genre: genreInput,
                releaseDate: new Date(releaseDateInput),
                price: priceInput,
              });
            }
          "
        >
          Submit
        </button>
      </form>
      <div className="flex flex-col items-center gap-10">
        <input
          v-model="searchInputText"
          type="text"
          placeholder="Search"
          class="input input-bordered w-full max-w-xs"
        />
        <div
          v-if="searchInputText && filteredGames?.length === 0"
          class="h-screen flex text-lg justify-center"
        >
          No game found
        </div>
        <button class="btn btn-outline btn-xs" @click="sortDates()">
          Sort by date
        </button>
        <div class="item game" v-for="game in filteredGames" :key="game.id">
          <div class="card w-96 bg-base-100 shadow-xl">
            <div class="card-body gap-5">
              <div className="flex justify-between">
                <h2 class="card-title">{{ game.title }}</h2>
                <button
                  @click="remove(game.id)"
                  class="btn btn-circle btn-xs btn-outline"
                >
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="h-3 w-3"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke="currentColor"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M6 18L18 6M6 6l12 12"
                    />
                  </svg>
                </button>
              </div>
              <span>Genre: {{ game.genre }}</span>
              <span
                >Release date:
                {{ game.releaseDate.toISOString().split("T")[0] }}</span
              >
              <span>{{ game.price ? `$${game.price}` : "Free to play" }}</span>
              <div class="card-actions justify-start gap-10 mt-5">
                <button
                  v-if="game.state === GameState.FOR_SALE"
                  class="btn btn-primary"
                  @click="buy(game.id)"
                >
                  Buy Now
                </button>
                <button
                  v-else-if="game.state === GameState.READY_TO_PLAY"
                  class="btn btn-primary"
                >
                  Play
                </button>
                <button
                  @click="addToWishlist(game.id)"
                  v-if="!game.wishlist && game.state === GameState.FOR_SALE"
                  class="btn btn-secondary text-xs"
                >
                  + Wishlist
                </button>
                <span
                  v-else-if="game.wishlist && game.state === GameState.FOR_SALE"
                  className="self-center text-xs"
                >
                  Added to wishlist
                </span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
