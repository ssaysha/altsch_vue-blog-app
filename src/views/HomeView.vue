<script setup>
import axios from 'axios'
import { onMounted, ref, computed } from 'vue'

const posts = ref([])
const loading = ref(false)
const error = ref(null)
const search = ref('')

/* PAGINATION STATE */
const currentPage = ref(1)
const perPage = 5

const fetchPosts = async () => {
  loading.value = true

  try {
    const response = await axios.get(
      'https://api.oluwasetemi.dev/posts'
    )

    posts.value = response.data.data || []
  } catch (err) {
    error.value = 'Failed to load posts'
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchPosts()
})

/* FILTERED POSTS */
const filteredPosts = computed(() => {
  return posts.value.filter(post =>
    post.title?.toLowerCase().includes(search.value.toLowerCase())
  )
})

/* PAGINATION LOGIC */
const totalPages = computed(() =>
  Math.ceil(filteredPosts.value.length / perPage)
)

const paginatedPosts = computed(() => {
  const start = (currentPage.value - 1) * perPage
  const end = start + perPage
  return filteredPosts.value.slice(start, end)
})

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++
  }
}

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--
  }
}
</script>

<template>
  <div class="page">

    <!-- HERO -->
    <header class="hero">
      <h1>📝 AyshaSS Blog Space</h1>
      <p>Explore articles, insights & ideas</p>

      <input
        v-model="search"
        class="search"
        placeholder="Search posts..."
      />
    </header>

    <!-- ERROR -->
    <div v-if="error" class="error">
      {{ error }}
    </div>

    <!-- LOADING -->
    <div v-if="loading" class="loading">
      Loading posts...
    </div>

    <!-- EMPTY -->
    <div v-else-if="paginatedPosts.length === 0" class="empty">
      No posts found 😢
    </div>

    <!-- POSTS -->
    <div v-else class="grid">
      <div
        v-for="post in paginatedPosts"
        :key="post.id"
        class="card"
      >
        <div class="tag">Blog Post</div>

        <h2>{{ post.title }}</h2>

        <p class="excerpt">
          {{ post.excerpt }}
        </p>

        <router-link
          class="btn"
          :to="`/post/${post.id}`"
        >
          Read More →
        </router-link>
      </div>
    </div>

    <!-- PAGINATION CONTROLS -->
    <div class="pagination" v-if="totalPages > 1">
      <button @click="prevPage" :disabled="currentPage === 1">
        Prev
      </button>

      <span>
        Page {{ currentPage }} of {{ totalPages }}
      </span>

      <button @click="nextPage" :disabled="currentPage === totalPages">
        Next
      </button>
    </div>

  </div>
</template>

<style scoped>
.page {
  max-width: 1000px;
  margin: auto;
  padding: 20px;
}

/* HERO */
.hero {
  text-align: center;
  margin-bottom: 30px;
}

.hero h1 {
  font-size: 32px;
}

.hero p {
  color: #666;
}

/* SEARCH */
.search {
  margin-top: 10px;
  padding: 10px;
  width: 100%;
  max-width: 400px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

/* GRID */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 15px;
}

/* CARD */
.card {
  background: white;
  border-radius: 12px;
  padding: 15px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);
  transition: 0.3s;
}

.card:hover {
  transform: translateY(-5px);
}

.tag {
  font-size: 12px;
  background: #eef6ff;
  color: #2b6cb0;
  padding: 4px 8px;
  display: inline-block;
  border-radius: 6px;
  margin-bottom: 10px;
}

.excerpt {
  color: #555;
  font-size: 14px;
  margin: 10px 0;
}

.btn {
  display: inline-block;
  padding: 8px 12px;
  background: #42b883;
  color: white;
  border-radius: 6px;
  text-decoration: none;
}

/* PAGINATION */
.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: center;
  gap: 15px;
  align-items: center;
}

.pagination button {
  padding: 8px 12px;
  border: none;
  background: #42b883;
  color: white;
  border-radius: 6px;
  cursor: pointer;
}

.pagination button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

/* STATES */
.loading,
.empty,
.error {
  text-align: center;
  margin-top: 20px;
}

.error {
  color: red;
}
</style>