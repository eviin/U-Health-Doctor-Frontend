<template>
  <div class="page">
    <h1>Patient Management</h1>
    <p class="muted">Create, search, view and delete patients (mock data).</p>

    <!-- Create Patient -->
    <div class="create-box">
      <input v-model="newPatientName" placeholder="Patient Name" />
      <button @click="createPatient">Create</button>
    </div>

    <!-- Search -->
    <div class="search-box">
      <input v-model="searchQuery" placeholder="Search patient..." />
    </div>

    <!-- Patient Table -->
    <table>
      <thead>
      <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Actions</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="patient in filteredPatients" :key="patient.id">
        <td>{{ patient.id }}</td>
        <td>{{ patient.name }}</td>
        <td>
          <router-link :to="`/doctor/patients/${patient.id}`">
            View
          </router-link>
          |
          <button @click="deletePatient(patient.id)">
            Delete
          </button>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface Patient {
  id: number
  name: string
}

const patients = ref<Patient[]>([
  { id: 1, name: 'Max Mustermann' },
  { id: 2, name: 'Anna Schmidt' },
])

const newPatientName = ref('')
const searchQuery = ref('')

function createPatient() {
  if (!newPatientName.value) return

  patients.value.push({
    id: Date.now(),
    name: newPatientName.value,
  })

  newPatientName.value = ''
}

function deletePatient(id: number) {
  patients.value = patients.value.filter(p => p.id !== id)
}

const filteredPatients = computed(() =>
  patients.value.filter(p =>
    p.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
)
</script>

<style scoped>
.page {
  max-width: 900px;
  margin: 30px auto;
}

.muted {
  color: #666;
}

.create-box,
.search-box {
  margin: 15px 0;
}

input {
  padding: 6px;
  margin-right: 8px;
}

button {
  padding: 6px 10px;
  cursor: pointer;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 15px;
}

th,
td {
  border: 1px solid #ddd;
  padding: 8px;
}
</style>

