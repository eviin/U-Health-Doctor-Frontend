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

    <!-- Header -->
    <section class="hero">
      <h1>Patient Management</h1>
      <p>Create, search and manage patients in U-Health.</p>
    </section>

    <!-- Content Card -->
    <section class="card">

      <!-- Create -->
      <div class="create-section">
        <input v-model="newPatient.first_name" placeholder="First name" />
        <input v-model="newPatient.last_name" placeholder="Last name" />
        <button @click="addPatient">Add</button>
      </div>

      <!-- Search -->
      <div class="search-section">
        <input v-model="search" placeholder="Search patient..." />
      </div>

      <!-- Table -->
      <table>
        <thead>
        <tr>
          <th>Name</th>
          <th style="width: 180px;">Actions</th>
        </tr>
        </thead>

        <tbody>
        <tr v-for="patient in filteredPatients" :key="patient.id">
          <td>{{ patient.first_name }} {{ patient.last_name }}</td>
          <td>
            <router-link to="/doctor/patient-detail">View</router-link>
            |
            <button class="danger-btn" @click="removePatient(patient.id)">Delete</button>
          </td>
        </tr>

        <tr v-if="filteredPatients.length === 0">
          <td colspan="2" class="muted">No patients found.</td>
        </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import api from '@/services/api'

const router = useRouter()

function logout() {
  localStorage.removeItem('token')
  router.push('/doctor/login')
}

type Patient = {
  id: number | string
  first_name?: string
  last_name?: string
  name?: string
  email?: string
}

const patients = ref<Patient[]>([])
const search = ref('')

const newPatient = ref({
  first_name: '',
  last_name: '',
  email: ''
})


onMounted(async () => {
  try {
    const res = await api.get('/admin/patients')
    patients.value = Array.isArray(res.data) ? res.data : (res.data.data ?? [])
    console.log('REAL PATIENTS:', patients.value)
  } catch (e: any) {
    console.error('PATIENTS ERROR:', e?.response?.status, e?.response?.data)
  }
})

const filteredPatients = computed(() => {
  const q = search.value.trim().toLowerCase()
  if (!q) return patients.value

  return patients.value.filter((p) => {
    const fullName = `${p.first_name ?? ''} ${p.last_name ?? ''}`.trim().toLowerCase()
    const name = (p.name ?? '').toLowerCase()
    const email = (p.email ?? '').toLowerCase()
    return fullName.includes(q) || name.includes(q) || email.includes(q)
  })
})


function addPatient() {
  const first = newPatient.value.first_name.trim()
  const last = newPatient.value.last_name.trim()
  const email = newPatient.value.email.trim()

  if (!first || !last) return

  patients.value.unshift({
    id: Date.now(),
    first_name: first,
    last_name: last,
    email
  })

  newPatient.value.first_name = ''
  newPatient.value.last_name = ''
  newPatient.value.email = ''
}

function removePatient(id: number | string) {
  patients.value = patients.value.filter(p => p.id !== id)
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

/* INPUTS */
.create-section,
.search-section {
  margin-bottom: 15px;
}

input {
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
  margin-right: 8px;
  width: 60%;
}

/* BUTTONS */
button {
  padding: 8px 12px;
  background-color: #1976d2;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: 0.2s;
}

button:hover {
  background-color: #145ea8;
}

.danger-btn {
  background: transparent;
  color: #c62828;
  border: none;
  padding: 0;
  font-weight: 500;
}

.danger-btn:hover {
  text-decoration: underline;
  background: transparent;
}

/* LINKS */
a {
  color: #1976d2;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

/* TABLE */
table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  padding: 10px;
  border-bottom: 1px solid #eee;
}

th {
  text-align: left;
  color: #1976d2;
}

tbody tr:hover {
  background-color: #f5f9ff;
}

.muted {
  color: #777;
  text-align: center;
  padding: 12px;
}
</style>
