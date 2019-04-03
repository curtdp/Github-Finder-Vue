<template>
  <div id="app" class="container mx-auto w-4/5 mt-8">
    <h1 class="text-center mb-4">Github Finder</h1>
    <form @submit.prevent="searchUser" class="flex mb-8">
      <input
        v-model="searchQuery"
        type="text"
        class="w-full border rounded p-2 mr-2"
        placeholder="Введите имя пользователя"
      >
      <button class="bg-blue text-white p-2 rounded">Найти</button>
    </form>

    <div v-if="isLoading" class="flex justify-center">
      <svg
        xmlns:svg="http://www.w3.org/2000/svg"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
        version="1.0"
        width="64px"
        height="64px"
        viewBox="0 0 128 128"
        xml:space="preserve"
      >
        <rect x="0" y="0" width="100%" height="100%" fill="#FFFFFF"></rect>
        <g>
          <path
            d="M75.4 126.63a11.43 11.43 0 0 1-2.1-22.65 40.9 40.9 0 0 0 30.5-30.6 11.4 11.4 0 1 1 22.27 4.87h.02a63.77 63.77 0 0 1-47.8 48.05v-.02a11.38 11.38 0 0 1-2.93.37z"
            fill="#000000"
            fill-opacity="1"
          ></path>
          <animateTransform
            attributeName="transform"
            type="rotate"
            from="0 64 64"
            to="360 64 64"
            dur="1800ms"
            repeatCount="indefinite"
          ></animateTransform>
        </g>
      </svg>
    </div>

    <div v-if="isLoaded && !isLoading">
      <h2>Пользователь найден</h2>
      <h3>{{ user.login }}</h3>
      <div class="flex">
        <div class="w-1/4 mr-4">
          <img :src="user.avatar_url" alt>
        </div>
        <div>user data</div>
      </div>
      <div>
        <h3>Репозитории</h3>
        <ul class="list-reset">
          <li v-for="(repo, index) in userRepos" :key="index" class="border rounded p-4 mb-4">
            <a class="block mb-2" :href="repo.html_url">{{repo.name}}</a>
            <div>⭐ {{ repo.stargazers_count}} 👀 {{ repo.watchers_count}}</div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { take } from "lodash-es";
import { setTimeout } from "timers";

export default {
  name: "app",
  data() {
    return {
      searchQuery: "curtdp",
      user: null,
      userRepos: null,
      isLoading: false,
      isLoaded: false
    };
  },
  methods: {
    searchUser() {
      this.isLoading = true;
      axios
        .get(`https://api.github.com/users/${this.searchQuery}`)
        .then(response => {
          // console.log(response);
          this.user = response.data;
          this.isLoaded = true;
          setTimeout(() => {
            this.isLoading = false;
          }, 1000);
          console.log(response);
          axios
            .get(`https://api.github.com/users/${this.searchQuery}/repos`)
            .then(response => {
              this.userRepos = take(response.data, 5);
            });
        })
        .catch(function(error) {
          // handle error
          console.log("error was: ", error);
        });
    }
  },
  created() {
    this.searchUser();
  },
  components: {}
};
</script>
