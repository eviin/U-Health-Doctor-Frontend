<template>
  <div class="page">

    <nav class="navbar">
      <router-link to="/doctor/login" class="nav-link">Login</router-link>
    </nav>

    <section class="hero">
      <h1>Welcome to U-Health</h1>
      <p>Reset your password to regain access to the Doctor Portal.</p>
    </section>

    <section class="login-section">
      <h2>Reset Password</h2>

      <form @submit.prevent="reset">
        <div class="form-group">
          <label>Email</label>
          <input v-model="email" type="email" required />
        </div>

        <button type="submit">Send Reset Link</button>
      </form>

      <div class="forgot-password">
        <router-link to="/doctor/login">
          Back to Login
        </router-link>
      </div>
    </section>

  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const email = ref('')

function reset() {
  alert(`Reset link sent to ${email.value} (mock)`)
}
</script>

<style scoped>
.page {
  font-family: Arial, sans-serif;
  background: white;
  min-height: 100vh;
}

.navbar {
  padding: 15px 40px;
  border-bottom: 1px solid #eee;
}

.nav-link {
  margin-right: 20px;
  text-decoration: none;
  color: #1976d2;
  font-weight: 500;
}

.hero {
  text-align: center;
  padding: 60px 20px 40px;
}

.hero h1 {
  color: #1976d2;
  font-size: 36px;
}

.hero p {
  color: #555;
}

.login-section {
  max-width: 400px;
  margin: 30px auto;
  padding: 20px;
  border: 1px solid #eee;
  border-radius: 8px;
}

.login-section h2 {
  text-align: center;
  margin-bottom: 20px;
  color: #1976d2;
}

.form-group {
  margin-bottom: 15px;
}

input {
  width: 100%;
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
}

button {
  width: 100%;
  padding: 10px;
  background-color: #1976d2;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}

.forgot-password {
  text-align: right;
  margin-top: 15px;
}
</style>


