<template>
  <div class="app">
    <h1>Posts Page</h1>
    <my-input v-model="searchQuery" placeholder="Search" />
    <div class="app__btns">
      <my-button @click="showDialog"> Create Post </my-button>
      <my-select v-model="selectedSort" :options="sortOptions" />
    </div>
    <my-dialog v-model:show="dialogVisible">
      <post-form @create="createPost" />
    </my-dialog>
    <post-list
      :posts="searchedAndSortedPosts"
      @remove="removePost"
      @edit="updatePost"
      v-if="!isPostsLoading"
    />
    <div v-else>Loading ...</div>
  </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import axios from "axios";
import MySelect from "@/components/UI/MySelect";
import MyInput from "@/components/UI/MyInput";
export default {
  components: {
    MyInput,
    MySelect,
    PostForm,
    PostList,
  },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: "",
      searchQuery: "",
      editedPost: {
        id: null,
        title: "",
        body: "",
      },
      sortOptions: [
        { value: "title", name: "Title" },
        { value: "body", name: "Description" },
      ],
    };
  },
  methods: {
    createPost(post) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    getPostById(id) {
      return this.posts.find((post) => post.id === id);
    },
    updatePost(temp) {
      console.log(this.posts, "temp");
      const postIndex = this.posts.find((post) => post.id === temp.id);
      console.log(postIndex, "index");
      this.posts[postIndex] = { ...this.editedPost };
      console.log("Post updated:", this.posts[postIndex]);
    },
    removePost(post) {
      this.posts = this.posts.filter((item) => item.id !== post.id);
    },
    showDialog() {
      this.dialogVisible = true;
    },
    async fetchPosts() {
      try {
        this.isPostsLoading = true;
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts?_limit=3"
        );
        this.posts = response.data;
      } catch (e) {
        console.log(e);
      } finally {
        this.isPostsLoading = false;
      }
    },
  },
  mounted() {
    this.fetchPosts();
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) =>
        post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
      );
    },
    searchedAndSortedPosts() {
      return this.sortedPosts.filter((post) =>
        post.title.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  padding: 20px;
}

.app__btns {
  display: flex;
  justify-content: space-between;
  margin: 15px 0;
}
</style>
