<template>
  <div>
    <template v-if="isLoadingPosts">
      Loading...
    </template>
    <template v-else>
      <template v-for="post in posts">
        <div
          :key="post._id"
          class="post"
        >
          <div
            @click="deletePost(post._id)"
            class="post-delete"
          >×</div>
          <header class="post-header">
            <h3 class="post-title">{{ post.title }}</h3>
            <p class="post-date">{{ post.date }}</p>
          </header>
          <p class="post-message">{{ post.message }}</p>
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
import dayjs from 'dayjs';
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
      axios
        .get('/posts')
        .then((res) => {
          this.posts = res.data.map((post) => {
            const date = dayjs(post.date).format('MMM DD YYYY HH:mm');
            return {
              ...post,
              date,
            };
          });
          this.isLoadingPosts = false;
        });
    },
    deletePost(postId) {
      axios
        .delete(`/delete-post/${postId}`, {
          params: { postId },
        })
        .then(() => {
          this.loadPosts();
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
          .then(() => {
            this.loadPosts();
            this.title = '';
            this.message = '';
          });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
$dark: rgb(44, 62, 80);
$dark-opacity: rgba(44, 62, 80, .5);

.send-post-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 20rem;
  margin: 2rem auto;

  .form-input {
    margin-bottom: 1rem;
    background-color: #fff;
    border: 2px solid $dark-opacity;
    padding: .5rem 1rem;
    font-size: 1rem;
    width: 20rem;
    color: $dark-opacity;
    transition: all .2s;
    &:focus {
      color: $dark;
      outline: unset;
    }
    &:hover {
      color: $dark;
    }
  }

  textarea {
    resize: unset;
    min-height: 10rem;
  }

  button.form-input {
    width: 10rem;
    &:hover {
      cursor: pointer;
    }
  }
}

.post {
  width: 100%;
  max-width: 40rem;
  margin: 0 auto 1rem;
  text-align: left;
  padding: 1rem;
  box-shadow: 0 8px 24px -9px $dark;
  position: relative;
  transition: all .2s;
  &-header {
    margin-bottom: 1rem;
  }
  &-title {
    margin: unset;
  }
  &-date {
    margin: unset;
    color: $dark-opacity;
    font-size: .8rem;
  }
  &-message {
    margin: unset;
  }
  &-delete {
    position: absolute;
    right: 1rem;
    top: 1rem;
    padding: .4rem;
    width: 1rem;
    height: 1rem;
    font-size: 1.4rem;
    display: flex;
    justify-content: center;
    align-items: center;
    color: $dark-opacity;
    opacity: 0;
    transition: all .2s;
    &:hover {
      cursor: pointer;
      color: $dark;
    }
  }
  &:hover {
    .post-delete {
      opacity: 1;
    }
  }
}
</style>
