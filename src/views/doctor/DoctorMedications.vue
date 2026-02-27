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
            <h1>Medication Management</h1>
            <p>Create, update and manage medications in U-Health.</p>
        </section>

        <section class="card">
            <!-- Create -->
            <div class="create-section">
                <input v-model="newMed.name" placeholder="Name" />
                <input v-model="newMed.description" placeholder="Description" />
                <button @click="addMedication">Add</button>
            </div>

            <p v-if="error" class="error">{{ error }}</p>

            <!-- Search -->
            <div class="search-section">
                <input
                        v-model="search"
                        placeholder="Search medication..."
                        @input="fetchMedications"
                />
            </div>

            <table>
                <thead>
                <tr>
                    <th>Name</th>
                    <th>Description</th>
                    <th style="width: 240px;">Actions</th>
                </tr>
                </thead>

                <tbody>
                <tr v-for="m in meds" :key="m.id">
                    <template v-if="editingId === m.id">
                        <td><input v-model="editMed.name" /></td>
                        <td><input v-model="editMed.description" /></td>
                        <td>
                            <button @click="updateMedication(m.id)">Save</button>
                            <button class="ghost-btn" @click="cancelEdit">Cancel</button>
                        </td>
                    </template>

                    <template v-else>
                        <td>{{ m.name }}</td>
                        <td>{{ m.description }}</td>
                        <td>
                            <button class="ghost-btn" @click="startEdit(m)">Edit</button>
                            |
                            <button class="danger-btn" @click="deleteMedication(m.id)">Delete</button>
                        </td>
                    </template>
                </tr>

                <tr v-if="meds.length === 0">
                    <td colspan="3" class="muted">No medications found.</td>
                </tr>
                </tbody>
            </table>
        </section>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import api from '@/services/api'

type Medication = {
    id: number
    name: string
    description: string
}

const meds = ref<Medication[]>([])
const search = ref('')
const error = ref('')

const newMed = ref({
    name: '',
    description: ''
})

const editingId = ref<number | null>(null)
const editMed = ref({
    name: '',
    description: ''
})

async function fetchMedications() {
    try {
        error.value = ''
        const res = await api.get('/admin/medications', {
            params: { q: search.value }
        })
        meds.value = Array.isArray(res.data) ? res.data : (res.data.data ?? [])
    } catch (e: any) {
        console.error('MEDICATION ERROR:', e?.response?.status, e?.response?.data)
        error.value = 'Could not load medications.'
    }
}

onMounted(fetchMedications)

async function addMedication() {
    try {
        error.value = ''

        const payload = {
            name: newMed.value.name.trim(),
            description: newMed.value.description.trim()
        }

        if (!payload.name || !payload.description) {
            error.value = 'Name and description are required.'
            return
        }

        await api.post('/admin/medications', payload)

        newMed.value.name = ''
        newMed.value.description = ''

        await fetchMedications()
    } catch (e: any) {
        console.error('ADD MEDICATION ERROR:', e?.response?.status, e?.response?.data)
        const msg = e?.response?.data?.message
        error.value = msg ? String(msg) : 'Could not create medication.'
    }
}

function startEdit(m: Medication) {
    editingId.value = m.id
    editMed.value = { name: m.name, description: m.description }
}

function cancelEdit() {
    editingId.value = null
}

async function updateMedication(id: number) {
    try {
        error.value = ''

        const payload = {
            name: editMed.value.name.trim(),
            description: editMed.value.description.trim()
        }

        await api.put(`/admin/medications/${id}`, payload)

        cancelEdit()
        await fetchMedications()
    } catch (e: any) {
        console.error('UPDATE MEDICATION ERROR:', e?.response?.status, e?.response?.data)
        error.value = 'Could not update medication.'
    }
}

async function deleteMedication(id: number) {
    try {
        error.value = ''
        await api.delete(`/admin/medications/${id}`)
        await fetchMedications()
    } catch (e: any) {
        console.error('DELETE MEDICATION ERROR:', e?.response?.status, e?.response?.data)
        error.value = 'Could not delete medication.'
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
.hero p { color: #555; }
.card { max-width: 1000px; margin: 20px auto 40px; padding: 20px; border-radius: 8px; background: white; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
.create-section, .search-section { margin-bottom: 15px; }
input { padding: 8px; border-radius: 6px; border: 1px solid #ccc; margin-right: 8px; }
button { padding: 8px 12px; background-color: #1976d2; color: white; border: none; border-radius: 6px; cursor: pointer; transition: 0.2s; }
button:hover { background-color: #145ea8; }
.ghost-btn { background: transparent; color: #1976d2; border: none; padding: 0; font-weight: 500; }
.ghost-btn:hover { text-decoration: underline; background: transparent; }
.danger-btn { background: transparent; color: #c62828; border: none; padding: 0; font-weight: 500; }
.danger-btn:hover { text-decoration: underline; background: transparent; }
.error { color: #c62828; margin-top: 10px; }
.muted { color: #777; text-align: center; padding: 12px; }
table { width: 100%; border-collapse: collapse; }
th, td { padding: 10px; border-bottom: 1px solid #eee; }
th { text-align: left; color: #1976d2; }
tbody tr:hover { background-color: #f5f9ff; }
</style>
