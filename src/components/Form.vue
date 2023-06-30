<script setup lang="ts">
import { ref } from "vue";
import { v4 as uuidv4 } from "uuid";

let titleInput = ref("");
let genreInput = ref("");
let releaseDateInput = ref("");
let priceInput = ref("");

const emits = defineEmits<{
  (
    e: "add",
    {
      id,
      title,
      releaseDate,
      genre,
    }: {
      id: string;
      title: string;
      releaseDate: Date;
      genre: string;
      price: string;
    }
  ): void;
}>();

const handleAdd = (
  id: string,
  title: string,
  releaseDate: Date,
  genre: string,
  price: string
) => {
  emits("add", { id, title, releaseDate, genre, price });
  titleInput.value = "";
  genreInput.value = "";
  releaseDateInput.value = "";
  priceInput.value = "";
};
</script>

<template>
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
          handleAdd(
            uuidv4(),
            titleInput,
            new Date(releaseDateInput),
            genreInput,
            priceInput
          );
        }
      "
    >
      Submit
    </button>
  </form>
</template>

<style scoped></style>
