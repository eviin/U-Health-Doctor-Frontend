<template>
  <div class="page">

    <!-- Top Navigation -->
    <nav class="navbar">
      <router-link to="/" class="nav-link">Home</router-link>
      <router-link to="/doctor/register" class="nav-link">Sign-Up</router-link>
    </nav>

    <!-- Welcome Section -->
    <section class="hero">
      <h1>Welcome to U-Health</h1>
      <p>Manage patients, treatments and appointments digitally and efficiently.</p>
    </section>

    <!-- Login Section -->
    <section class="login-section">
      <h2>Doctor Login</h2>

      <form @submit.prevent="login">
        <div class="form-group">
          <label>Email</label>
          <input v-model="email" type="email" required />
        </div>

        <div class="form-group">
          <label>Password</label>
          <input v-model="password" type="password" required />
        </div>

        <!-- Forgot Password -->
        <div class="forgot-password">
          <router-link to="/doctor/reset-password">
            Forgot password?
          </router-link>
        </div>

        <button type="submit">Login</button>
      </form>
    </section>

    <!-- Doctor Menu -->
    <section class="doctor-menu">
      <router-link to="/doctor/patients" class="menu-card">
        Patient Management
      </router-link>

      <router-link to="/doctor/dashboard" class="menu-card">
        Dashboard
      </router-link>
    </section>

  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const email = ref('')
const password = ref('')
const router = useRouter()

function login() {
  router.push('/doctor/dashboard')
}
</script>

<style scoped>
.page {
  font-family: Arial, sans-serif;
  background: white;
  min-height: 100vh;
}

/* NAVBAR */
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

/* HERO */
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

/* LOGIN */
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
  margin-bottom: 15px;
}

.forgot-password a {
  font-size: 14px;
  color: #1976d2;
  text-decoration: none;
}

/* DOCTOR MENU */
.doctor-menu {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin: 50px 0;
}

.menu-card {
  padding: 20px 30px;
  border: 1px solid #1976d2;
  border-radius: 8px;
  text-decoration: none;
  color: #1976d2;
  font-weight: 500;
  transition: 0.2s;
}

.menu-card:hover {
  background: #1976d2;
  color: white;
}
</style>





