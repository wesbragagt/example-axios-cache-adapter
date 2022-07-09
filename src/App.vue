<script setup lang="ts">
import axios from 'axios'
import { setupCache } from 'axios-cache-adapter'
import { ref } from 'vue';

const seconds = (time: number) => time * 1000
const minutes = (time: number) => seconds(time) * 60 

// Create `axios-cache-adapter` instance
const cache = setupCache({
  maxAge: minutes(15)
})

// Create `axios` instance passing the newly created `cache.adapter`
const api = axios.create({
  adapter: cache.adapter
})

const todos = ref<null | any[]>(null);

// Send a GET request to some REST api
const getData = () => {
  api({
    url: 'https://jsonplaceholder.typicode.com/todos',
    method: 'get'
  }).then(async (response) => {
    // Do something fantastic with response.data \o/
    console.log('Request response:', response)

    // Interacting with the store, see `localForage` API.
    const store = cache.store

    console.log('Cache store length:', store)
    todos.value = response.data;
  })
}
</script>

<template>
  <h1>Trying axios cache adapter</h1>
  <button @click="getData">Get Data</button>
  <pre v-if="todos">{{JSON.stringify(todos, undefined, 2)}}</pre>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
