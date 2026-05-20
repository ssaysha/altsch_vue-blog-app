<script setup>
import { ref, onErrorCaptured } from 'vue'

const hasError = ref(false)
const errorMessage = ref('')

onErrorCaptured((err) => {
  hasError.value = true
  errorMessage.value = err.message || 'Something went wrong'
  return false
})
</script>

<template>
  <div v-if="hasError" class="error">
    <h2>Oops! Something went wrong 😢</h2>
    <p>{{ errorMessage }}</p>
  </div>

  <slot v-else />
</template>

<style scoped>
.error {
  text-align: center;
  padding: 30px;
  color: red;
}
</style>