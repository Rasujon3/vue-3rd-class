<script setup>
import { ref, defineProps, computed, defineEmits, watch } from 'vue'
import moment from 'moment'

const props = defineProps({
  post: {
    type: Object,
    required: true
  },
  index: {
    type: Number,
    required: true
  }
})
const post = ref(props.post)

const emit = defineEmits({
  addComment: null,
  deleteComment: (index, commentIndex) => {
    if (typeof index === 'number' && typeof commentIndex === 'number') {
      return true
    }
    return false
  }
})

const reversedComments = computed(() => {
  return [...post.value.comments].reverse()
})

watch(
  () => post.value.comments.length,
  (val) => {
    if (val >= 3) {
      alert('Congatulations! You have reached 3 comments!')
    }
  }
)
</script>

<template>
  <div class="comments mb-3">
    <form @submit.prevent="emit('addComment', index)">
      <div class="comments-input d-flex mb-3">
        <input
          type="text"
          v-model="post.commentFormData"
          class="form-control form-control-sm me-2"
          placeholder="Write Comment"
          required
        />
        <button class="btn btn-sm btn-success"><i class="bi bi-send"></i></button>
      </div>

      <template v-if="post.showCommentSection">
        <template v-for="(comment, commentIndex) in reversedComments" :key="comment.id">
          <hr v-if="commentIndex" class="my-2 very-low-opacity" />
          <div class="comment mb-3 ms-3">
            <h6 class="card-title small">
              {{ comment.userName }}
              <span
                @click="emit('deleteComment', index, reversedComments.length - (commentIndex + 1))"
                class="text-danger float-end cursor-pointer"
                >X</span
              >
            </h6>
            <p class="card-subtitle mb-1 text-body-secondary small">
              {{ moment(comment.date).fromNow() }}
            </p>
            <p class="card-text small">{{ comment.content }}</p>
          </div>
        </template>
      </template>
    </form>
  </div>
</template>
