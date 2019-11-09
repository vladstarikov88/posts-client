<template>
  <div>
    <template v-for="post in posts">
      <div :key="post.id">
        <h3>{{ post.author }}</h3>
        <p>{{ post.message }}</p>
      </div>
    </template>
  </div>
</template>

<script>
import axios from '@/plugins/axios';

export default {
  data() {
    return {
      posts: [],
    };
  },
  mounted() {
    axios
      .get('/posts')
      .then((res) => {
        this.posts = res.data;
      });
  },
};
</script>

<style>

</style>
