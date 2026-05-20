<script setup>
import axios from 'axios'
import { ref, watch } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const post = ref(null)
const loading = ref(false)
const error = ref(null)

const fetchPost = async () => {
  loading.value = true
  error.value = null

  try {
    const response = await axios.get(
      `https://api.oluwasetemi.dev/posts/${route.params.id}`
    )

    post.value = response.data.data || response.data
  } catch (err) {
    error.value = 'Post not found'
  } finally {
    loading.value = false
  }
}

watch(
  () => route.params.id,
  () => fetchPost(),
  { immediate: true }
)
</script>

<template>
  <div class="container">

    <div v-if="loading">Loading...</div>

    <div v-else-if="error" class="error">
      {{ error }}
    </div>

    <div v-else-if="post" class="card">
      <h1>{{ post.title || 'Untitled' }}</h1>

      <p class="content">
        {{ post.content || post.body || post.description || 'No content' }}
      </p>

      <button @click="router.push('/')">
        ← Back Home
      </button>
    </div>

  </div>
</template>

<style scoped>
.container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
}

.card {
  border: 1px solid #ddd;
  padding: 20px;
  border-radius: 12px;
}

.content {
  margin-top: 15px;
  line-height: 1.7;
}

button {
  margin-top: 20px;
  padding: 10px 14px;
  background: #42b883;
  color: white;
  border: none;
  border-radius: 6px;
}

.error {
  color: red;
}
</style>