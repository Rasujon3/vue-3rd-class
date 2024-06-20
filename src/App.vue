<script setup>
import { ref, computed, watch, provide } from 'vue'
import moment from 'moment'
import Posts from './components/Posts.vue'
import CreatePost from './components/CreatePost.vue'
import layoutContent from './components/LayoutContent.vue'

const posts = ref([
  {
    id: 1,
    title: 'Post 1',
    content:
      'This is a wider card with supporting text below as a natural lead-in to additional content. This content is a little bit longer.',
    likes: 0,
    comments: [],
    date: '2024-05-24 11:00:00',
    commentFormData: '',
    showCommentSection: false
  },
  {
    id: 2,
    title: 'Post 2',
    content:
      'This is a wider card with supporting text below as a natural lead-in to additional content. This content is a little bit longer.',
    likes: 0,
    comments: [],
    date: '2024-05-24 11:00:00',
    commentFormData: '',
    showCommentSection: false
  },
  {
    id: 3,
    title: 'Post 3',
    content:
      'This is a wider card with supporting text below as a natural lead-in to additional content. This content is a little bit longer.',
    likes: 0,
    comments: [],
    date: '2024-05-24 11:00:00',
    commentFormData: '',
    showCommentSection: false
  }
])

const firstName = 'John'
const lastName = 'Doe'

const userName = computed(() => {
  return firstName + ' ' + lastName
})
// const userName = ref('Asif Mallik');

function increaseLikeCount(index) {
  posts.value[index].likes++
}

function deletePost(index) {
  posts.value.splice(index, 1)
}

// function postCreate(event) {
//   event.preventDefault();
function postCreate(post) {
  posts.value.push(post)
}

function addComment(index) {
  posts.value[index].comments.push({
    id: posts.value[index].comments.length + 1,
    userName: userName.value,
    content: posts.value[index].commentFormData,
    date: moment().format('YYYY-MM-DD HH:mm:ss')
  })

  posts.value[index].commentFormData = ''
  posts.value[index].showCommentSection = true
}

function deleteComment(postIndex, commentIndex) {
  posts.value[postIndex].comments.splice(commentIndex, 1)
}

watch(
  () => posts.value.length,
  (newValue, oldValue) => {
    if (newValue > oldValue) {
      alert('A new post has been created!')
    }
  },
  { immediate: true }
)

watch(
  () => posts.value,
  () => {
    posts.value.forEach((post) => {
      if (post.likes === 10 && !post.likeConfirmation) {
        post.likeConfirmation = true
        alert('A post has reached 10 likes!')
      }
    })
  },
  { deep: true }
)

provide('posts', posts)
const currentComponent = ref('posts')
</script>

<template>
  <layoutContent
    :userName="userName"
    @changeComponent="(componet) => (currentComponent = componet)"
    :currentComponent="currentComponent"
  >
    <CreatePost
      v-if="currentComponent === 'create-post'"
      @postCreate="postCreate"
      :postCount="posts.length"
      class="bg-light p-4"
      style="border-radius: 5px"
    />

    <Posts
      v-else-if="currentComponent === 'posts'"
      @increaseLikeCount="increaseLikeCount"
      @deletePost="deletePost"
      @addComment="addComment"
      @deleteComment="deleteComment"
    />
  </layoutContent>
</template>

<style>
.cursor-pointer {
  cursor: pointer;
}

.very-low-opacity {
  opacity: 0.1;
}
</style>
