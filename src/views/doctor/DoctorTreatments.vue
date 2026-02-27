<template>
  <div class="page">
    <nav class="navbar">
      <router-link to="/doctor/dashboard" class="nav-link">Dashboard</router-link>
      <router-link to="/doctor/patients" class="nav-link">Patients</router-link>
      <router-link to="/doctor/bookings" class="nav-link">Bookings</router-link>
      <router-link to="/doctor/diseases" class="nav-link">Diseases</router-link>
      <router-link to="/doctor/medications" class="nav-link">Medications</router-link>
      <router-link to="/doctor/treatments" class="nav-link">Treatments</router-link>
    </nav>

    <section class="hero">
      <h1>Treatment Records</h1>
      <p>Create and manage patient treatments.</p>
    </section>

    <section class="card">
      <!-- Create Treatment -->
      <div class="create-section">
        <input v-model.number="newTreatment.patient_id" type="number" placeholder="Patient ID" />
        <input v-model="newTreatment.diagnosis" placeholder="Diagnosis" />
        <input v-model="newTreatment.type_of_treatment" placeholder="Type of treatment" />
        <input v-model="newTreatment.date_of_treatment" type="date" />

        <input v-model="diseasesInput" placeholder="Diseases (comma separated)" />

        <div class="medication-section">
          <input v-model="medicationInput.name" placeholder="Medication name" />
          <input v-model="medicationInput.dosis" placeholder="Dosis" />
          <input v-model.number="medicationInput.amount" type="number" placeholder="Amount" />
        </div>

        <button @click="addTreatment">Add Treatment</button>
      </div>

      <p v-if="error" class="error">{{ error }}</p>

      <!-- List Treatments -->
      <table>
        <thead>
        <tr>
          <th>Patient</th>
          <th>Diagnosis</th>
          <th>Date</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="t in treatments" :key="t.treatment_id">
          <td>{{ t.patient_id }}</td>
          <td>{{ t.diagnosis }}</td>
          <td>{{ t.date_of_treatment }}</td>
        </tr>

        <tr v-if="treatments.length === 0">
          <td colspan="3" class="muted">No treatments found.</td>
        </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import api from '@/services/api'

const treatments = ref<any[]>([])
const error = ref('')

const diseasesInput = ref('')
const medicationInput = ref({
  name: '',
  dosis: '',
  amount: 0
})

const newTreatment = ref({
  patient_id: 0,
  diagnosis: '',
  type_of_treatment: '',
  date_of_treatment: ''
})

async function fetchTreatments() {
  try {
    const res = await api.get('/admin/treatments')
    treatments.value = Array.isArray(res.data) ? res.data : (res.data.data ?? [])
  } catch (e: any) {
    error.value = 'Could not load treatments.'
  }
}

onMounted(fetchTreatments)

async function addTreatment() {
  try {
    error.value = ''

    const diseasesArray = diseasesInput.value
        ? diseasesInput.value.split(',').map(d => d.trim())
        : []

    const medicationsArray = medicationInput.value.name
        ? [{
          medication_name: medicationInput.value.name,
          dosis: medicationInput.value.dosis,
          amount: medicationInput.value.amount
        }]
        : []

    const payload = {
      patient_id: newTreatment.value.patient_id,
      diagnosis: newTreatment.value.diagnosis,
      type_of_treatment: newTreatment.value.type_of_treatment,
      date_of_treatment: newTreatment.value.date_of_treatment,
      diseases: diseasesArray,
      medications: medicationsArray
    }

    await api.post('/admin/treatments', payload)

    newTreatment.value = {
      patient_id: 0,
      diagnosis: '',
      type_of_treatment: '',
      date_of_treatment: ''
    }

    diseasesInput.value = ''
    medicationInput.value = { name: '', dosis: '', amount: 0 }

    await fetchTreatments()
  } catch (e: any) {
    console.error(e?.response?.data)
    error.value = 'Could not create treatment.'
  }
}
</script>

<style scoped>
.page { font-family: Arial, sans-serif; background: white; min-height: 100vh; }
.navbar { padding: 15px 40px; border-bottom: 1px solid #eee; }
.nav-link { margin-right: 20px; text-decoration: none; color: #1976d2; font-weight: 500; }
.hero { text-align: center; padding: 60px 20px 30px; }
.hero h1 { color: #1976d2; font-size: 34px; }
.card { max-width: 1000px; margin: 20px auto; padding: 20px; border-radius: 8px; background: white; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
.create-section { margin-bottom: 20px; }
input { padding: 8px; border-radius: 6px; border: 1px solid #ccc; margin-right: 8px; margin-bottom: 8px; }
button { padding: 8px 12px; background-color: #1976d2; color: white; border: none; border-radius: 6px; cursor: pointer; }
button:hover { background-color: #145ea8; }
.error { color: #c62828; margin-top: 10px; }
.muted { color: #777; text-align: center; padding: 12px; }
table { width: 100%; border-collapse: collapse; }
th, td { padding: 10px; border-bottom: 1px solid #eee; }
th { text-align: left; color: #1976d2; }
</style>
