<template>
  <div class="wrapper">
    <form action="" @submit.prevent="postData">
      <input type="email" v-model="email" required />
      <input v-maska="number" data-maska="##-##-##" />
      <button>click</button>
    </form>

    <div class="content" v-if="!loader">
      <div class="item" v-for="(item, i) in users" :key="i">
        <a :href="`mailto:${item.email}`">Email:{{ item.email }}</a>
        <a :href="`tel:${item.number}`">Tel:{{ item.number }}</a>
      </div>
    </div>
    <div class="loader" v-else></div>
  </div>
</template>



<script setup>
import { vMaska } from "maska";
import { ref, reactive, computed } from "vue";
const email = ref("");
const number = reactive({});
let users = ref([]);
let abortController = null;
const loader = ref(false)
async function postData() {
  loader.value = true
  if (abortController) {
    abortController.abort();
  }
  abortController = new AbortController();
  const signal = abortController.signal;
  let res = await fetch("http://localhost:3000/search/user", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      email: email.value,
      number: Number(number.unmasked),
    }),
    signal,
  });
  loader.value = false
  let data = await res.json();
  users.value = data;
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.loader {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background: #0000007e;
  display: flex;
  align-items: center;
  justify-content: center;
}
.loader::after{
  content: '';
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border-top: 2px solid lime;
  animation: loader infinite linear 1s;
}
@keyframes loader{
  0%{
    transform: rotate(0);
  }
  100%{
    transform: rotate(360deg);
  }
}
.content {
  max-width: 1600px;
  width: 100%;
  margin: 0;
  padding: 0;
}
button,
input {
  padding: 15px;
  font-size: 18px;
}
.content {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 30px;
}
.item {
  border: 2px solid red;
  padding: 20px;
  display: flex;
  flex-direction: column;
}
</style>