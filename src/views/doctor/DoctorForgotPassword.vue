<template>
  <div class="page">
    <nav class="navbar">
      <router-link to="/doctor/login" class="nav-link">Back to Login</router-link>
    </nav>

    <section class="hero">
      <h1>Forgot Password</h1>
      <p>Enter your email and we will send you a reset link.</p>
    </section>

    <section class="card">
      <input v-model="email" type="email" placeholder="Email" />
      <button @click="sendLink">Send reset link</button>

      <p v-if="message" class="success">{{ message }}</p>
      <p v-if="error" class="error">{{ error }}</p>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import api from '@/services/api'

const email = ref('')
const message = ref('')
const error = ref('')

async function sendLink() {
  try {
    message.value = ''
    error.value = ''

    await api.post('/forgot-password', { email: email.value.trim() })
    message.value = 'Reset link sent. Please check your email.'
  } catch (e: any) {
    const msg = e?.response?.data?.message
    error.value = msg ? String(msg) : 'Could not send reset link.'
  }
}
</script>

<style scoped>
.page { font-family: Arial, sans-serif; background: white; min-height: 100vh; }
.navbar { padding: 15px 40px; border-bottom: 1px solid #eee; }
.nav-link { text-decoration: none; color: #1976d2; font-weight: 500; }
.hero { text-align: center; padding: 60px 20px 30px; }
.hero h1 { color: #1976d2; font-size: 34px; }
.card { max-width: 520px; margin: 20px auto; padding: 20px; border-radius: 8px; background: white; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
input { width: 100%; padding: 10px; border-radius: 6px; border: 1px solid #ccc; margin-bottom: 12px; }
button { padding: 10px 12px; background-color: #1976d2; color: white; border: none; border-radius: 6px; cursor: pointer; width: 100%; }
button:hover { background-color: #145ea8; }
.error { color: #c62828; margin-top: 10px; }
.success { color: #2e7d32; margin-top: 10px; }
</style>
