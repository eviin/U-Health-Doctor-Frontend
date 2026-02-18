<template>
  <div class="page">
    <h1>Patient Detail</h1>

    <div class="card">
      <p><strong>ID:</strong> {{ patientId }}</p>
      <p><strong>Name:</strong> {{ patient.name }}</p>
      <p><strong>Date of Birth:</strong> {{ patient.dob }}</p>
    </div>

    <div class="actions">
      <router-link :to="`/doctor/patients/${patientId}/treatments`">
        View Treatment Records
      </router-link>

      |
      <router-link to="/doctor/patients">
        Back to Patients
      </router-link>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useRoute } from 'vue-router'

const route = useRoute()
const patientId = route.params.id

// Mock patient data
const patient = {
  name: 'Max Mustermann',
  dob: '1990-05-12',
}
</script>

<style scoped>
.page {
  max-width: 800px;
  margin: 30px auto;
}

.card {
  border: 1px solid #ddd;
  padding: 15px;
  margin-top: 15px;
}

.actions {
  margin-top: 20px;
}
</style>

