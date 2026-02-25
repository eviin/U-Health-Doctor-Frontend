<template>
  <div class="page">

    <nav class="navbar">
      <router-link to="/doctor/dashboard" class="nav-link">Dashboard</router-link>
      <router-link to="/doctor/patients" class="nav-link">Patients</router-link>
      <router-link to="/doctor/bookings" class="nav-link">Bookings</router-link>
      <router-link to="/doctor/medications" class="nav-link">Medications</router-link>
    </nav>

    <section class="hero">
      <h1>Disease Management</h1>
      <p>Create, search and manage diseases.</p>
    </section>

    <section class="card">

      <!-- Create -->
      <h2>Create Disease</h2>
      <div class="form-group">
        <input v-model="newName" placeholder="Disease name" />
        <input v-model="newDescription" placeholder="Description" />
        <button @click="addDisease">Add</button>
      </div>

      <!-- Search -->
      <h2>Search</h2>
      <input v-model="search" placeholder="Search disease..." />

      <!-- Table -->
      <h2>Diseases</h2>
      <table>
        <thead>
        <tr>
          <th>Name</th>
          <th>Description</th>
          <th>Actions</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="d in filteredDiseases" :key="d.id">
          <td>{{ d.name }}</td>
          <td>{{ d.description }}</td>
          <td>
            <button @click="selectDisease(d)">View</button>
            |
            <button @click="removeDisease(d.id)">Delete</button>
          </td>
        </tr>
        </tbody>
      </table>

      <!-- View -->
      <div v-if="selectedDisease" class="details">
        <h3>Disease Details</h3>
        <p><strong>Name:</strong> {{ selectedDisease.name }}</p>
        <p><strong>Description:</strong> {{ selectedDisease.description }}</p>
        <button @click="selectedDisease = null">Close</button>
      </div>

    </section>

  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface Disease {
  id: number
  name: string
  description: string
}

const diseases = ref<Disease[]>([
  { id: 1, name: 'Hypertension', description: 'High blood pressure' },
  { id: 2, name: 'Diabetes', description: 'Metabolic disease' }
])

const newName = ref('')
const newDescription = ref('')
const search = ref('')
const selectedDisease = ref<Disease | null>(null)

function addDisease() {
  if (!newName.value.trim()) return

  diseases.value.push({
    id: Date.now(),
    name: newName.value,
    description: newDescription.value
  })

  newName.value = ''
  newDescription.value = ''
}

function removeDisease(id: number) {
  diseases.value = diseases.value.filter(d => d.id !== id)
  if (selectedDisease.value?.id === id) {
    selectedDisease.value = null
  }
}

function selectDisease(d: Disease) {
  selectedDisease.value = d
}

const filteredDiseases = computed(() =>
  diseases.value.filter(d =>
    d.name.toLowerCase().includes(search.value.toLowerCase())
  )
)
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
  max-width: 800px;
  margin: 20px auto 40px;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

.form-group input {
  margin-right: 8px;
  margin-bottom: 10px;
  padding: 6px;
}

button {
  padding: 6px 10px;
  background-color: #1976d2;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 8px;
  border-bottom: 1px solid #eee;
}

.details {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #eee;
}
</style>
