<template>
  <div>
    <template v-if="isLoadingPosts">
      Loading...
    </template>
    <template v-else>
      <template v-for="post in posts">
        <div :key="post._id">
          <h3>{{ post.title }}</h3>
          <p>{{ post.message }}</p>
        </div>
      </template>
    </template>

    <hr>
    <h2>White new note</h2>
    <form
      class="send-post-form"
      @submit.prevent="submit"
    >
      <input
        type="text"
        placeholder="Title"
        v-model="title"
        class="form-input"
      >
      <textarea
        v-model="message"
        placeholder="Message"
        class="form-input"
      />
      <button
        type="submit"
        class="form-input"
      >
        send data
      </button>
    </form>
  </div>
</template>

<script>
import axios from '@/plugins/axios';

export default {
  data() {
    return {
      posts: [],
      title: '',
      message: '',
      isLoadingPosts: false,
    };
  },
  mounted() {
    this.loadPosts();
  },
  methods: {
    loadPosts() {
      this.isLoadingPosts = true;

      axios
        .get('/posts')
        .then((res) => {
          this.posts = res.data;
          this.isLoadingPosts = false;
        })
        .catch((error) => {
          console.error(error);
        });
    },
    submit() {
      const newPost = {
        title: this.title,
        message: this.message,
      };

      if (newPost.title && newPost.message) {
        axios
          .post('/add-post', newPost)
          .then((res) => {
            console.log(res);
            this.loadPosts();
          });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.send-post-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 20rem;
  margin: 2rem auto;

  .form-input {
    margin-bottom: 1rem;
    background-color: #fff;
    border: 2px solid rgba(44, 62, 80, .5);
    padding: .5rem 1rem;
    font-size: 1rem;
    width: 20rem;
    color: rgba(44, 62, 80, .5);
    transition: all .2s;
    &:focus {
      color: rgb(44, 62, 80);
      outline: unset;
    }
    &:hover {
      color: rgb(44, 62, 80);
    }
  }
  input {
  }

  textarea {
    resize: unset;
    min-height: 10rem;
  }

  button.form-input {
    width: 10rem;
    &:hover {
      cursor: pointer;
      background-color: rgba(0, 0, 0, .05)
    }
  }
}
</style>
