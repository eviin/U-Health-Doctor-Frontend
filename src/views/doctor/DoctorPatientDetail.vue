<template>
  <div class="page">

    <nav class="navbar">
      <router-link to="/doctor/dashboard" class="nav-link">Dashboard</router-link>
      <router-link to="/doctor/patients" class="nav-link">Patients</router-link>
    </nav>

    <section class="hero">
      <h1>Patient Information</h1>
      <p>View and manage patient treatment records.</p>
    </section>

    <section class="card">

      <!-- Patient Info -->
      <h2>Patient Details</h2>
      <div class="patient-info">
        <p><strong>Name:</strong> Anna Müller</p>
        <p><strong>Date of Birth:</strong> 01.01.1999</p>
        <p><strong>Email:</strong> anna.müller@gmail.com</p>
        <p><strong>Insurance Number:</strong> A1234567</p>
      </div>

      <hr />

      <!-- Treatment Record Management -->
      <h2>Treatment Records</h2>

      <div class="form-row">
        <input v-model="newTreatment" placeholder="Treatment description" />
        <input v-model="newDate" type="date" />
        <button @click="addTreatment">Add</button>
      </div>

      <table>
        <thead>
        <tr>
          <th>Date</th>
          <th>Description</th>
          <th>Actions</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="t in treatments" :key="t.id">
          <td>{{ t.date }}</td>
          <td>{{ t.description }}</td>
          <td>
            <button @click="removeTreatment(t.id)">Delete</button>
          </td>
        </tr>
        </tbody>
      </table>

    </section>

  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface Treatment {
  id: number
  date: string
  description: string
}

const treatments = ref<Treatment[]>([
  { id: 1, date: '2026-02-20', description: 'Routine check-up' },
  { id: 2, date: '2026-02-22', description: 'Blood pressure monitoring' }
])

const newTreatment = ref('')
const newDate = ref('')

function addTreatment() {
  if (!newTreatment.value || !newDate.value) return

  treatments.value.push({
    id: Date.now(),
    date: newDate.value,
    description: newTreatment.value
  })

  newTreatment.value = ''
  newDate.value = ''
}

function removeTreatment(id: number) {
  treatments.value = treatments.value.filter(t => t.id !== id)
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
  max-width: 900px;
  margin: 20px auto 40px;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

.patient-info p {
  margin: 6px 0;
}

.form-row input {
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
</style>
