<template>
  <div class="page">
    <nav class="navbar">
      <router-link to="/doctor/dashboard" class="nav-link">Dashboard</router-link>
      <router-link to="/doctor/patients" class="nav-link">Patients</router-link>
      <router-link to="/doctor/bookings" class="nav-link">Bookings</router-link>
      <router-link to="/doctor/diseases" class="nav-link">Diseases</router-link>
      <router-link to="/doctor/medications" class="nav-link">Medications</router-link>
    </nav>

    <section class="hero">
      <h1>Change Password</h1>
      <p>Update your account password.</p>
    </section>

    <section class="card">
      <div class="form-group">
        <label>Current Password</label>
        <input v-model="currentPassword" type="password" />
      </div>

      <div class="form-group">
        <label>New Password</label>
        <input v-model="newPassword" type="password" />
      </div>

      <div class="form-group">
        <label>Confirm New Password</label>
        <input v-model="confirmPassword" type="password" />
      </div>

      <button @click="changePassword">Update Password</button>

      <p v-if="error" class="error">{{ error }}</p>
      <p v-if="success" class="success">{{ success }}</p>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const currentPassword = ref('')
const newPassword = ref('')
const confirmPassword = ref('')
const error = ref('')
const success = ref('')

onMounted(() => {
  const token = localStorage.getItem('token')
  if (!token) {
    router.push('/doctor/login')
  }
})

function changePassword() {
  error.value = ''
  success.value = ''

  if (!currentPassword.value || !newPassword.value || !confirmPassword.value) {
    error.value = 'Please fill all fields.'
    return
  }

  if (newPassword.value !== confirmPassword.value) {
    error.value = 'New passwords do not match.'
    return
  }

  success.value = 'Password updated successfully (UI only).'

  currentPassword.value = ''
  newPassword.value = ''
  confirmPassword.value = ''
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
  padding: 60px 20px 30px;
}

.hero h1 {
  color: #1976d2;
}

.card {
  max-width: 450px;
  margin: 20px auto 40px;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

.form-group {
  margin-bottom: 12px;
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

.error {
  color: #c62828;
  margin-top: 10px;
}

.success {
  color: #2e7d32;
  margin-top: 10px;
}
</style>

