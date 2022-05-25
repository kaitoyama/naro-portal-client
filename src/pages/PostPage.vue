<script setup>
import axios from "axios";
import moment from "moment";
import router from "../router.js";
import { ref, onMounted } from "vue";
const post = ref("");
const props = defineProps({
  number: {
    type: String,
    required: true,
  },
});
const recentPost = ref();
onMounted(async () => {
  const res = await axios.get("/api/recent/" + props.number);
  recentPost.value = res.data;
});
const sendPost = () => {
  axios.post("/api/post", {
    text: post.value,
  });
  setTimeout(() => {
    router.go({ path: router.currentRoute.path, force: true });
  }, 100);
};

function date(value) {
  return moment(value).fromNow();
}
</script>

<template>
  <div>
    <h1>Postしよう!</h1>
  </div>
  <div>
    <p>Let's Post</p>
    <input v-model="post" type="text" />
  </div>
  <div v-if="post != ''">
    <button @click="sendPost">Go!</button>
  </div>
  <div v-else>
    <button>Go!</button>
  </div>
  <div v-if="recentPost">
    <div v-for="p in recentPost" :key="p.timeStamp">
      <ul>
        <li>User:{{ p.username }}</li>
        <li>{{ p.text }}</li>
        created at
        {{
          date(p.timeStamp)
        }}
      </ul>
    </div>
  </div>
  <div v-else>投稿が見つかりませんでした</div>
</template>
