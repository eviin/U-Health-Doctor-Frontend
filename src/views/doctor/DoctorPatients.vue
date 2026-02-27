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

        <input
          v-model.number="newPatient.user_id"
          type="number"
          min="1"
          placeholder="User ID"
        />

        <input
          v-model.number="newPatient.age"
          type="number"
          min="0"
          placeholder="Age"
        />

        <input v-model="newPatient.sex" placeholder="Sex (e.g. male/female)" />
        <input v-model="newPatient.location" placeholder="Location" />

        <button @click="addPatient">Add</button>
      </div>

      <p v-if="error" class="error">{{ error }}</p>

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
        <tr v-for="patient in filteredPatients" :key="patient.id ?? patient.user_id">
          <td>{{ patient.first_name }} {{ patient.last_name }}</td>
          <td>
            <router-link :to="`/doctor/patient-detail/${patient.id}`">View</router-link>
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
  id?: number | string
  first_name?: string
  last_name?: string
  user_id?: number
  age?: number
  sex?: string
  location?: string
}

const patients = ref<Patient[]>([])
const search = ref('')
const error = ref('')

const newPatient = ref({
  user_id: 1,
  first_name: '',
  last_name: '',
  age: 0,
  sex: '',
  location: ''
})

async function fetchPatients() {
  try {
    const res = await api.get('/patients')
    patients.value = Array.isArray(res.data) ? res.data : (res.data.data ?? [])
  } catch (e: any) {
    console.error('PATIENTS ERROR:', e?.response?.status, e?.response?.data)
  }
}

onMounted(async () => {
  await fetchPatients()
})

const filteredPatients = computed(() => {
  const q = search.value.trim().toLowerCase()
  if (!q) return patients.value

  return patients.value.filter((p) => {
    const fullName = `${p.first_name ?? ''} ${p.last_name ?? ''}`.trim().toLowerCase()
    const loc = `${p.location ?? ''}`.toLowerCase()
    return fullName.includes(q) || loc.includes(q)
  })
})

async function addPatient() {
  try {
    error.value = ''

    const payload = {
      user_id: Number(newPatient.value.user_id),
      first_name: newPatient.value.first_name.trim(),
      last_name: newPatient.value.last_name.trim(),
      age: Number(newPatient.value.age),
      sex: newPatient.value.sex.trim(),
      location: newPatient.value.location.trim()
    }

    if (!payload.user_id || payload.user_id < 1) {
      error.value = 'User ID is required.'
      return
    }
    if (!payload.first_name || !payload.last_name) {
      error.value = 'First name and last name are required.'
      return
    }
    if (!payload.sex || !payload.location) {
      error.value = 'Sex and location are required.'
      return
    }

    await api.post('/admin/patients', payload)

    // reset inputs
    newPatient.value.first_name = ''
    newPatient.value.last_name = ''
    newPatient.value.age = 0
    newPatient.value.sex = ''
    newPatient.value.location = ''

    // reload list
    await fetchPatients()
  } catch (e: any) {
    console.error('ADD PATIENT ERROR:', e?.response?.status, e?.response?.data)
    const status = e?.response?.status
    const msg = e?.response?.data?.message
    error.value = status ? `Could not add patient (${status})${msg ? ': ' + msg : ''}` : 'Could not add patient.'
  }
}

async function removePatient(id: number | string | undefined) {
  if (id === undefined || id === null) return

  try {
    error.value = ''
    await api.delete(`/patients/${id}`)
    await fetchPatients()
  } catch (e: any) {
    console.error('DELETE PATIENT ERROR:', e?.response?.status, e?.response?.data)
    error.value = 'Could not delete patient.'
  }
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
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
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

.error {
  color: #c62828;
  margin: 10px 0 0;
}
</style>
