<script setup>
import { ref, defineProps, computed, defineEmits, inject } from 'vue'
import moment from 'moment'
import CommentSection from './CommentSection.vue'

// const props = defineProps(['posts']);
// const props = defineProps({
//   posts: {
//     type: Array,
//     required: true
//   }
// })
// const posts = ref(props.posts)
const posts = inject(['posts'])

const reversedPosts = computed(() => {
  return [...posts.value].reverse()
})
// function reversedPosts() {
//   console.log('Just Testing');
//   return [...posts.value].reverse();
// }

// const emit = defineEmits(['increaseLikeCount', 'deletePost', 'addComment', 'deleteComment'])
const emit = defineEmits({
  increaseLikeCount: null,
  deletePost: null,
  addComment: null,
  deleteComment: (index, commentIndex) => {
    if (typeof index === 'number' && typeof commentIndex === 'number') {
      return true
    }
    return false
  }
})

function deletePost(index) {
  emit('deletePost', index)
}
</script>

<template>
  <div class="posts mt-4">
    <div class="container">
      <div class="nav nav-tabs mb-4">
        <h3>
          Posts <span :class="{ 'text-danger': posts.length <= 0 }">{{ posts.length }}</span>
        </h3>
      </div>

      <div class="row">
        <div class="col-md-4" v-for="(post, index) in reversedPosts" :key="post.id">
          <div class="card mb-4">
            <div class="card-body">
              <h5 class="card-title">{{ post.title }}</h5>
              <h6 class="card-subtitle mb-2 text-body-secondary">
                {{ moment(post.date).fromNow() }}
              </h6>
              <p class="card-text">{{ post.content }}</p>
              <p class="card-text">
                <span :style="{ color: post.likes >= 10 ? 'green' : 'black' }">
                  <small v-if="post.likes > 1">{{ post.likes }} likes, </small>
                  <small v-else-if="post.likes > 0">{{ post.likes }} like, </small>
                  <small v-else>No Likes, </small>
                </span>
                <small
                  :style="{ color: post.comments.length >= 3 ? 'green' : 'black' }"
                  class="cursor-pointer"
                  @click="post.showCommentSection = !post.showCommentSection"
                >
                  {{ post.comments.length }} comments</small
                >
              </p>

              <CommentSection
                :post="post"
                :index="reversedPosts.length - (index + 1)"
                @addComment="(index) => emit('addComment', index)"
                @deleteComment="(index, commentIndex) => emit('deleteComment', index, commentIndex)"
                v-model:comment="post.commentFormData"
              />

              <button
                class="btn btn-sm btn-primary"
                @click="emit('increaseLikeCount', reversedPosts.length - (index + 1))"
              >
                Like
              </button>
              <button
                class="btn btn-sm btn-danger float-end"
                @click="deletePost(reversedPosts.length - (index + 1))"
              >
                <i class="bi bi-trash"></i>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
