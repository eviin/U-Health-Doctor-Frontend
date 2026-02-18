<template>
  <div class="container">
    <h1>Doctor Login</h1>

    <form @submit.prevent="login">
      <div>
        <label>Email</label>
        <input v-model="email" type="email" required />
      </div>

      <div>
        <label>Password</label>
        <input v-model="password" type="password" required />
      </div>

      <button type="submit">Login</button>
    </form>

    <router-link to="/doctor/reset-password">
      Forgot password?
    </router-link>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const email = ref('')
const password = ref('')
const router = useRouter()

function login() {
  // mock login: just navigate to dashboard
  router.push('/doctor/dashboard')
}
</script>

<style scoped>
.container {
  max-width: 420px;
  margin: 48px auto;
  display: grid;
  gap: 12px;
}
label { display:block; margin-bottom: 4px; }
input { width: 100%; padding: 8px; }
button { padding: 10px; cursor: pointer; }
</style>

