<script setup>
import { ref, reactive } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";
const movies = ref(items);

function updateRating(movieIndex, rating) {
  movies.value[movieIndex].rating = rating;
}

const modalState = ref(false)

const openModal=()=>{
    modalState.value = true
}

const closeModal=()=>{
    modalState.value = false
}


const validations = reactive({
  name: "required",
  genres: "required",
});

const form = reactive({
  name: null,
  description: null,
  image: null,
  inTheaters: false,
  genres: [],
});

const errors = reactive({
  name: null,
  description: null,
  image: null,
  inTheaters: null,
  genres: null,
});

const genres = reactive([
  { text: "Drama", value: "Drama" },
  { text: "Crime", value: "Crime" },
  { text: "Action", value: "Action" },
  { text: "Comedy", value: "Comedy" },
]);


const validationRules = (rule) => {
  if (rule === "required") return /^ *$/;

  return null;
};

function validate() {
  let valid = true;
  clearErrors();
  for (const [field, rule] of Object.entries(validations)) {
    const validation = validationRules(rule);

    if (validation) {
      if (validation.test(form[field] || "")) {
        errors[field] = `${field} is ${rule}`;
        valid = false;
      }
    }
  }

  return valid;
}

function addMovie(){

  if(validate()){
  const movie ={
    name: form.name,
    description: form.description,
    genres: form.genres,
    image: form.image,
    inTheaters: form.inTheaters
  }
  movies.value.push(movie)
  closeModal()
}
}


function cleanUpForm() {
  form.name = null;
  form.description = null;
  form.image = null;
  form.genres = [];
  form.inTheaters = false;
  clearErrors();
}

function clearErrors() {
  errors.name = null;
  errors.description = null;
  errors.image = null;
  errors.genres = null;
  errors.inTheaters = null;
}
</script>

<template>
      <div class="navigation-bar">
          <div class="navigation-content">
            <button class="btn" @click="openModal">
              Create Movie
            </button>
         </div>
      </div>
  <div class="app">

    <div class="form-modal" v-if="modalState">
      <div class="form-container">
        <form @submit.prevent="addMovie">

        <div class="form-item">
          <label for="name" class="label">Name</label>
          <input type="textbox" name="name" id="name" v-model="form.name"/>
          <p class="errorform">{{errors.name}}</p>
        </div>

        <div class="form-item">
          <label for="description" class="label">Description</label>
          <textarea id="description" name="description" rows="3" cols="33" v-model="form.description">
          </textarea>
          <p class="errorform">{{errors.description}}</p>
        </div>

        <div class="form-item">
          <label for="img" class="label">Image</label>
          <input type="textbox" name="img" id="img" v-model="form.image"/>
          <p class="errorform">{{errors.image}}</p>
        </div>


        <div class="form-item">
          <select id="pet-select" v-model="form.genres"
          multiple>
            <option
                v-for="option in genres"
                :key="option.value"
                :value="option.value"
              >
                {{ option.text }}
              </option>
          </select>
          <p class="errorform">{{errors.genres}}</p>
        </div>

        <div class="form-item-in-threaters">
          <input type="checkbox" id="in-threaters" name="in-threaters" checked v-model="form.inTheaters"/>
          <label for="in-threaters" class="label">In theathers</label>
          <p class="errorform">{{errors.inTheaters}}</p>
        </div>


        <div class="form-behaviors">
          <button class="btn" @click="closeModal">
              Cancel 
          </button>
          <button class="btn" type="submit">
              Submit 
          </button>
        </div>
      </form>
      </div>
    </div>

    <div class="movie-list">
      <div
        class="movie-item"
        v-for="(movie, movieIndex) in movies"
        :key="movie.id"
      >
        <div class="movie-item-image-wrapper">
          <div class="movie-item-star-wrapper">
            <StarIcon
              class="movie-item-star-rating-icon"
              :class="[movie.rating ? 'text-yellow-500' : 'text-gray-500']"
            />
            <div class="movie-item-star-content-wrapper">
              <span
                v-if="movie.rating"
                class="movie-item-star-content-rating-rated"
              >
                {{ movie.rating }}
              </span>
              <span v-else class="movie-item-star-content-rating-not-rated">
                -
              </span>
            </div>
          </div>
          <img :src="movie.image" class="movie-item-image" alt="" />
        </div>

        <div class="movie-item-content-wrapper">
          <div class="movie-item-title-wrapper">
            <h3 class="movie-item-title">{{ movie.name }}</h3>
            <div class="movie-item-genres-wrapper">
              <span
                v-for="genre in movie.genres"
                :key="`${movie.id}-${genre}`"
                class="movie-item-genre-tag"
                >{{ genre }}</span
              >
            </div>
          </div>
          <div class="movie-item-description-wrapper">
            <p class="movie-item-description">{{ movie.description }}</p>
          </div>
          <div class="movie-item-rating-wrapper">
            <span class="movie-item-rating-text">
              Rating: ({{ movie.rating }}/5)
            </span>

            <div class="movie-item-star-icon-wrapper">
              <button
                v-for="star in 5"
                :key="star"
                class="movie-item-star-icon-button"
                :class="[
                  star <= movie.rating ? 'text-yellow-500' : 'text-gray-500',
                ]"
                :disabled="star === movie.rating"
                @click="updateRating(movieIndex, star)"
              >
                <StarIcon class="movie-item-star-icon" />
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
