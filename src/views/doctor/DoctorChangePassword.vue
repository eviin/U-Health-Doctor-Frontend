<template>
  <div class="container">
    <h1>Reset Password</h1>
    <p class="muted">Enter your email to receive a reset link (mock UI).</p>

    <form @submit.prevent="submit">
      <div>
        <label>Email</label>
        <input v-model="email" type="email" required />
      </div>

      <button type="submit">Send reset link</button>
    </form>

    <div class="links">
      <router-link to="/doctor/login">Back to Login</router-link>
    </div>

    <p v-if="message" class="msg">{{ message }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const email = ref('')
const message = ref('')

function submit() {
  message.value = `Reset link sent to: ${email.value} (mock)`
}
</script>

<style scoped>
.container { max-width: 420px; margin: 48px auto; display: grid; gap: 12px; }
label { display:block; margin-bottom: 4px; }
input { width: 100%; padding: 8px; }
button { padding: 10px; cursor: pointer; }
.muted { color:#666; margin-top: -6px; }
.links { margin-top: 6px; }
.msg { padding: 10px; border: 1px solid #e5e5e5; border-radius: 8px; }
</style>

