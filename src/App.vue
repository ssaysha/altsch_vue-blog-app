<script setup>
import ErrorBoundary from './components/ErrorBoundary.vue'
</script>

<template>
  <ErrorBoundary>
    <router-view v-slot="{ Component }">
      <Suspense>
        <template #default>
          <component :is="Component" />
        </template>

        <template #fallback>
          <div class="loading-screen">
            <div class="spinner"></div>
            <p>Loading page...</p>
          </div>
        </template>
      </Suspense>
    </router-view>
  </ErrorBoundary>
</template>

<style scoped>
.loading-screen {
  text-align: center;
  padding: 50px;
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #ddd;
  border-top: 4px solid #42b883;
  border-radius: 50%;
  margin: auto;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0); }
  100% { transform: rotate(360deg); }
}
</style>