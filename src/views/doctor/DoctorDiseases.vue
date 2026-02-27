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
      <h1>Disease Management</h1>
      <p>Create, update and manage diseases in U-Health.</p>
    </section>

    <section class="card">
      <!-- Create -->
      <div class="create-section">
        <input v-model="newDisease.name" placeholder="Name" />
        <input v-model="newDisease.description" placeholder="Description" />
        <input v-model="newDisease.icd_code" placeholder="ICD Code" />
        <button @click="addDisease">Add</button>
      </div>

      <p v-if="error" class="error">{{ error }}</p>

      <!-- Search -->
      <div class="search-section">
        <input
            v-model="search"
            placeholder="Search disease..."
            @input="fetchDiseases"
        />
      </div>

      <table>
        <thead>
        <tr>
          <th>Name</th>
          <th>Description</th>
          <th>ICD Code</th>
          <th style="width: 240px;">Actions</th>
        </tr>
        </thead>

        <tbody>
        <tr v-for="d in diseases" :key="d.id">
          <template v-if="editingId === d.id">
            <td><input v-model="editDisease.name" /></td>
            <td><input v-model="editDisease.description" /></td>
            <td><input v-model="editDisease.icd_code" /></td>
            <td>
              <button @click="updateDisease(d.id)">Save</button>
              <button class="ghost-btn" @click="cancelEdit">Cancel</button>
            </td>
          </template>

          <template v-else>
            <td>{{ d.name }}</td>
            <td>{{ d.description }}</td>
            <td>{{ d.icd_code }}</td>
            <td>
              <button class="ghost-btn" @click="startEdit(d)">Edit</button>
              |
              <button class="danger-btn" @click="deleteDisease(d.id)">Delete</button>
            </td>
          </template>
        </tr>

        <tr v-if="diseases.length === 0">
          <td colspan="4" class="muted">No diseases found.</td>
        </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import api from '@/services/api'

type Disease = {
  id: number
  name: string
  description: string
  icd_code: string
}

const diseases = ref<Disease[]>([])
const search = ref('')
const error = ref('')

const newDisease = ref({
  name: '',
  description: '',
  icd_code: ''
})

const editingId = ref<number | null>(null)
const editDisease = ref({
  name: '',
  description: '',
  icd_code: ''
})

async function fetchDiseases() {
  try {
    error.value = ''
    const res = await api.get('/admin/diseases', {
      params: { q: search.value }
    })
    diseases.value = Array.isArray(res.data) ? res.data : (res.data.data ?? [])
  } catch (e: any) {
    console.error('DISEASE ERROR:', e?.response?.status)
    error.value = 'Could not load diseases.'
  }
}

onMounted(fetchDiseases)

async function addDisease() {
  try {
    error.value = ''

    await api.post('/admin/diseases', newDisease.value)

    newDisease.value = { name: '', description: '', icd_code: '' }
    await fetchDiseases()
  } catch (e: any) {
    const msg = e?.response?.data?.message
    error.value = msg ?? 'Could not create disease.'
  }
}

function startEdit(d: Disease) {
  editingId.value = d.id
  editDisease.value = {
    name: d.name,
    description: d.description,
    icd_code: d.icd_code
  }
}

function cancelEdit() {
  editingId.value = null
}

async function updateDisease(id: number) {
  try {
    error.value = ''
    await api.put(`/admin/diseases/${id}`, editDisease.value)
    cancelEdit()
    await fetchDiseases()
  } catch (e: any) {
    error.value = 'Could not update disease.'
  }
}

async function deleteDisease(id: number) {
  try {
    error.value = ''
    await api.delete(`/admin/diseases/${id}`)
    await fetchDiseases()
  } catch (e: any) {
    error.value = 'Could not delete disease.'
  }
}
</script>

<style scoped>
.page { font-family: Arial, sans-serif; background: white; min-height: 100vh; }
.navbar { padding: 15px 40px; border-bottom: 1px solid #eee; }
.nav-link { margin-right: 20px; text-decoration: none; color: #1976d2; font-weight: 500; }
.nav-link:hover { text-decoration: underline; }
.hero { text-align: center; padding: 60px 20px 30px; }
.hero h1 { color: #1976d2; font-size: 34px; }
.card { max-width: 1000px; margin: 20px auto 40px; padding: 20px; border-radius: 8px; background: white; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
input { padding: 8px; border-radius: 6px; border: 1px solid #ccc; margin-right: 8px; }
button { padding: 8px 12px; background-color: #1976d2; color: white; border: none; border-radius: 6px; cursor: pointer; }
button:hover { background-color: #145ea8; }
.ghost-btn { background: transparent; color: #1976d2; border: none; }
.danger-btn { background: transparent; color: #c62828; border: none; }
.error { color: #c62828; margin-top: 10px; }
.muted { color: #777; text-align: center; padding: 12px; }
table { width: 100%; border-collapse: collapse; }
th, td { padding: 10px; border-bottom: 1px solid #eee; }
th { text-align: left; color: #1976d2; }
</style>
