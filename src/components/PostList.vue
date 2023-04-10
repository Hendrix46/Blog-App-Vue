<template>
  <div v-if="posts.length > 0">
    <h3>Post List</h3>
    <TransitionGroup name="post-list">
      <post-item
        :post="post"
        v-for="post in posts"
        :key="post.id"
        @remove="$emit('remove', post)"
        @edit="$emit('edit', post)"
      />
    </TransitionGroup>
  </div>

  <h3 v-else style="color: red">There is not any posts available yet!</h3>
</template>

<script>
import PostItem from "@/components/PostItem.vue";
export default {
  components: {
    PostItem,
  },
  props: {
    posts: {
      type: Array,
      required: true,
    },
  },
};
</script>

<style scoped>
.post-list-move,
.post-list-enter-active,
.post-list-leave-active {
  transition: all 0.5s ease;
}

.post-list-enter-from,
.post-list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.post-list-leave-active {
  position: absolute;
}
</style>
