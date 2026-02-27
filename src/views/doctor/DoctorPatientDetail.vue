<template>
  <div class="page">
    <!-- Navigation -->
    <nav class="navbar">
      <router-link to="/doctor/dashboard" class="nav-link">Dashboard</router-link>
      <router-link to="/doctor/patients" class="nav-link">Patients</router-link>
      <router-link to="/doctor/bookings" class="nav-link">Bookings</router-link>
      <router-link to="/doctor/diseases" class="nav-link">Diseases</router-link>
      <router-link to="/doctor/medications" class="nav-link">Medications</router-link>
      <router-link to="/doctor/treatments" class="nav-link">Treatments</router-link>
    </nav>

    <section class="hero">
      <h1>Patient Details</h1>
      <p>View patient information.</p>
    </section>

    <section class="card">
      <p v-if="loading" class="muted">Loading...</p>
      <p v-else-if="error" class="error">{{ error }}</p>

      <div v-else>
        <div class="row">
          <span class="label">Name</span>
          <span class="value">{{ fullName }}</span>
        </div>

        <div class="row" v-if="patientEmail">
          <span class="label">Email</span>
          <span class="value">{{ patientEmail }}</span>
        </div>

        <div class="row" v-if="patientId">
          <span class="label">Patient ID</span>
          <span class="value">{{ patientId }}</span>
        </div>

        <div class="actions">
          <router-link to="/doctor/patients" class="back-link">← Back to Patients</router-link>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import api from '@/services/api'

type Patient = {
  id?: number | string
  first_name?: string
  last_name?: string
  name?: string
  email?: string
}

const route = useRoute()

const loading = ref(true)
const error = ref('')
const patient = ref<Patient | null>(null)

const patientId = computed(() => patient.value?.id ?? '')
const patientEmail = computed(() => patient.value?.email ?? '')

const fullName = computed(() => {
  if (!patient.value) return ''
  if (patient.value.name) return patient.value.name

  const first = patient.value.first_name ?? ''
  const last = patient.value.last_name ?? ''
  return `${first} ${last}`.trim()
})

onMounted(async () => {
  try {
    error.value = ''
    loading.value = true

    const id = route.params.id
    const res = await api.get(`/patients/${id}`)

    patient.value = res.data?.data ?? res.data
  } catch (e: any) {
    console.error('PATIENT DETAIL ERROR:', e?.response?.status, e?.response?.data)
    error.value = 'Could not load patient details.'
  } finally {
    loading.value = false
  }
})
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

.nav-link:hover {
  text-decoration: underline;
}

/* HERO */
.hero {
  text-align: center;
  padding: 60px 20px 30px;
}

.hero h1 {
  color: #1976d2;
  font-size: 34px;
}

.hero p {
  color: #555;
}

/* CARD */
.card {
  max-width: 700px;
  margin: 20px auto 40px;
  padding: 20px;
  border-radius: 8px;
  background: white;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

.row {
  display: flex;
  justify-content: space-between;
  padding: 10px 0;
  border-bottom: 1px solid #eee;
}

.label {
  color: #1976d2;
  font-weight: 600;
}

.value {
  color: #333;
}

.actions {
  margin-top: 16px;
}

.back-link {
  color: #1976d2;
  text-decoration: none;
}

.back-link:hover {
  text-decoration: underline;
}

.muted {
  color: #777;
  text-align: center;
  padding: 12px;
}

.error {
  color: #c62828;
  text-align: center;
}
</style>
