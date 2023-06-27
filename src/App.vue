<script setup lang="ts">
import { ref } from "vue";

enum GameState {
  FOR_SALE = "FOR_SALE",
  READY_TO_PLAY = "READY_TO_PLAY",
}

interface Games {
  id: string;
  title: string;
  releaseDate: Date;
  genre: string;
  price?: number;
  discountPercentage?: number;
  images?: {
    url: string;
    previewUrl: string;
    alt: string;
  };
  state: GameState;
  wishlist: boolean;
}

const games = ref<Games[] | null>(null);
games.value = [
  {
    id: "1",
    title: "League of Legends",
    releaseDate: new Date(2009, 9, 27),
    genre: "Battle arena",
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: "2",
    title: "DOTA",
    releaseDate: new Date(2013, 6, 9),
    genre: "Strategy",
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: "3",
    title: "The Sims 4",
    releaseDate: new Date(2014, 8, 2),
    genre: "Simulation",
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: "4",
    title: "Inside",
    releaseDate: new Date(2019, 7, 29),
    genre: "Platform, Puzzle, Adventure",
    price: 100,
    state: GameState.FOR_SALE,
    wishlist: false,
  },
  {
    id: "5",
    title: "Limbo",
    releaseDate: new Date(2010, 8, 21),
    genre: "Platform, Puzzle, Adventure",
    price: 80,
    state: GameState.FOR_SALE,
    wishlist: false,
  },
];

const addToWishlist = (id: string) => {
  if (games.value) {
    const updatedGames = games.value.map((item) => {
      if (item.id === id) {
        item.wishlist = true;
      }
      return item;
    });
    games.value = updatedGames;
  }
};

const buy = (id: string) => {
  if (games.value) {
    const updatedGames = games.value.map((item) => {
      if (item.id === id) {
        item.state = GameState.READY_TO_PLAY;
        item.wishlist = false;
      }
      return item;
    });
    games.value = updatedGames;
  }
};

let searchInput = ref("");

const filteredGames = () => {
  if (games.value) {
    return games.value.filter((game) =>
      game.title.toLowerCase().includes(searchInput.value.toLowerCase())
    );
  }
};

console.log(filteredGames);
</script>

<template>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css"
  />
  <div className="h-full bg-slate-800 flex flex-col items-center py-10 gap-10">
    <input
      v-model="searchInput"
      type="text"
      placeholder="Search"
      class="input input-bordered w-full max-w-xs"
    />
    <div
      v-if="searchInput && filteredGames()?.length === 0"
      class="h-screen flex text-lg justify-center"
    >
      No game found
    </div>
    <div
      class="item game"
      v-if="searchInput"
      v-for="game in filteredGames()"
      :key="game.id"
      className="h-screen"
    >
      <div class="card w-96 bg-base-100 shadow-xl">
        <div class="card-body gap-5">
          <div className="flex justify-between">
            <h2 class="card-title">{{ game.title }}</h2>
            <span>{{ game.price ? `$${game.price}` : "Free to play" }}</span>
          </div>
          <span>Genre: {{ game.genre }}</span>
          <span
            >Release date:
            {{ game.releaseDate.toISOString().split("T")[0] }}</span
          >
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

    <ul v-if="games && !searchInput" className="flex flex-col gap-10">
      <li v-for="item in games" :key="item.id">
        <div class="card w-96 bg-base-100 shadow-xl">
          <div class="card-body gap-5">
            <div className="flex justify-between">
              <h2 class="card-title">{{ item.title }}</h2>
              <span>{{ item.price ? `$${item.price}` : "Free to play" }}</span>
            </div>
            <span>Genre: {{ item.genre }}</span>
            <span
              >Release date:
              {{ item.releaseDate.toISOString().split("T")[0] }}</span
            >
            <div class="card-actions justify-start gap-10 mt-5">
              <button
                v-if="item.state === GameState.FOR_SALE"
                class="btn btn-primary"
                @click="buy(item.id)"
              >
                Buy Now
              </button>
              <button
                v-else-if="item.state === GameState.READY_TO_PLAY"
                class="btn btn-primary"
              >
                Play
              </button>
              <button
                @click="addToWishlist(item.id)"
                v-if="!item.wishlist && item.state === GameState.FOR_SALE"
                class="btn btn-secondary text-xs"
              >
                + Wishlist
              </button>
              <span
                v-else-if="item.wishlist && item.state === GameState.FOR_SALE"
                className="self-center text-xs"
              >
                Added to wishlist
              </span>
            </div>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<style scoped></style>
