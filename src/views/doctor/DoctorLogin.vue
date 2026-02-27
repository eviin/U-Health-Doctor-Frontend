<template>
  <div class="page">
    <section class="hero">
      <h1>Welcome to U-Health</h1>
      <p>Doctor Access Portal</p>
    </section>

    <section class="card">
      <h2>Sign In</h2>

      <input
          v-model="email"
          type="email"
          placeholder="Email"
      />

      <input
          v-model="password"
          type="password"
          placeholder="Password"
      />

      <router-link
          to="/doctor/forgot-password"
          class="forgot-link"
      >
        Forgot password?
      </router-link>

      <button @click="login">Login</button>

      <p v-if="error" class="error">{{ error }}</p>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import api from '@/services/api'

const router = useRouter()

const email = ref('')
const password = ref('')
const error = ref('')

async function login() {
  try {
    error.value = ''

    const res = await api.post('/login', {
      email: email.value.trim(),
      password: password.value
    })

    // Token speichern
    const token = res.data?.token
    if (!token) {
      error.value = 'Login failed.'
      return
    }

    localStorage.setItem('token', token)

    // Weiterleitung zum Dashboard
    router.push('/doctor/dashboard')
  } catch (e: any) {
    const msg = e?.response?.data?.message
    error.value = msg ? String(msg) : 'Invalid credentials.'
  }
}
</script>

<style scoped>
.page {
  font-family: Arial, sans-serif;
  background: white;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.hero {
  text-align: center;
  margin-bottom: 30px;
}

.hero h1 {
  color: #1976d2;
  font-size: 36px;
  margin-bottom: 8px;
}

.hero p {
  color: #555;
}

.card {
  max-width: 420px;
  margin: 0 auto;
  padding: 30px;
  border-radius: 8px;
  background: white;
  box-shadow: 0 4px 14px rgba(0,0,0,0.06);
  display: flex;
  flex-direction: column;
}

.card h2 {
  margin-bottom: 20px;
  color: #1976d2;
  text-align: center;
}

input {
  padding: 10px;
  border-radius: 6px;
  border: 1px solid #ccc;
  margin-bottom: 12px;
}

button {
  padding: 10px;
  background-color: #1976d2;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: 0.2s;
  margin-top: 10px;
}

button:hover {
  background-color: #145ea8;
}

.forgot-link {
  font-size: 14px;
  color: #1976d2;
  text-decoration: none;
  margin-bottom: 10px;
  text-align: right;
}

.forgot-link:hover {
  text-decoration: underline;
}

.error {
  color: #c62828;
  margin-top: 12px;
  text-align: center;
}
</style>
