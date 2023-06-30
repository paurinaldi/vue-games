<script setup lang="ts">
import { Games } from "../App.vue";

enum GameState {
  FOR_SALE = "FOR_SALE",
  READY_TO_PLAY = "READY_TO_PLAY",
}

defineProps<{
  games: Games[] | null;
  filteredGames: Games[] | null | undefined;
  searchInputText: string;
  sortDates: () => void;
}>();

const emits = defineEmits<{
  (e: "addToWishlist", id: string): void;
  (e: "buy", id: string): void;
  (e: "remove", id: string): void;
}>();

const handleAddToWishlist = (id: string) => {
  emits("addToWishlist", id);
};

const handleBuy = (id: string) => {
  emits("buy", id);
};

const handleRemove = (id: string) => {
  emits("remove", id);
};
</script>

<template>
  <div className="flex flex-col items-center gap-10">
    <input
      :value="searchInputText"
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
              @click="handleRemove(game.id)"
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
              @click="handleBuy(game.id)"
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
              @click="handleAddToWishlist(game.id)"
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
</template>
