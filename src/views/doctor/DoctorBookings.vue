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
            <h1>Booking Management</h1>
            <p>Create and manage available time slots in U-Health.</p>
        </section>

        <section class="card">
            <!-- Create Timeslot -->
            <div class="create-section">
                <input v-model="newSlot.start" type="datetime-local" />
                <input v-model="newSlot.end" type="datetime-local" />
                <button @click="addTimeSlot">Add</button>
            </div>

            <p v-if="error" class="error">{{ error }}</p>

            <!-- Booked filter -->
            <div class="filter-row">
                <label class="checkbox">
                    <input type="checkbox" v-model="showBookedOnly" @change="fetchSlots" />
                    Show booked slots only
                </label>
            </div>

            <!-- Search -->
            <div class="search-section">
                <input v-model="search" placeholder="Search by date/time..." />
            </div>

            <table>
                <thead>
                <tr>
                    <th>Start</th>
                    <th>End</th>
                    <th>Status</th>
                    <th style="width: 240px;">Actions</th>
                </tr>
                </thead>

                <tbody>
                <tr v-for="slot in filteredSlots" :key="slot.booking_id ?? slot.id">
                    <td>{{ formatDateTime(slot.time_slot_start) }}</td>
                    <td>{{ formatDateTime(slot.time_slot_end) }}</td>
                    <td>{{ slot.status === 0 ? 'Available' : 'Booked' }}</td>

                    <td>
                        <template v-if="editingKey === (slot.booking_id ?? slot.id)">
                            <div class="edit-row">
                                <input v-model="editSlot.start" type="datetime-local" />
                                <input v-model="editSlot.end" type="datetime-local" />
                            </div>
                            <button @click="updateTimeSlot(slot.booking_id ?? slot.id)">Save</button>
                            <button class="ghost-btn" @click="cancelEdit">Cancel</button>
                        </template>

                        <template v-else>
                            <button class="ghost-btn" @click="startEdit(slot)">Edit</button>
                            |
                            <button class="danger-btn" @click="deleteTimeSlot(slot.booking_id ?? slot.id)">Delete</button>
                        </template>
                    </td>
                </tr>

                <tr v-if="filteredSlots.length === 0">
                    <td colspan="4" class="muted">No time slots found.</td>
                </tr>
                </tbody>
            </table>
        </section>
    </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import api from '@/services/api'

type TimeSlot = {
    id?: number
    booking_id?: number
    patient_id: number | null
    time_slot_start: string
    time_slot_end: string
    status: number
}

const slots = ref<TimeSlot[]>([])
const search = ref('')
const error = ref('')
const showBookedOnly = ref(false)

const newSlot = ref({
    start: '',
    end: ''
})

const editingKey = ref<number | null>(null)
const editSlot = ref({ start: '', end: '' })

function toIsoStringFromDatetimeLocal(value: string) {
    // "YYYY-MM-DDTHH:MM" -> "YYYY-MM-DDTHH:MM:00"
    if (!value) return ''
    return value.length === 16 ? `${value}:00` : value
}

function formatDateTime(value: string) {
    if (!value) return ''
    return value.replace('T', ' ').replace('Z', '')
}

async function fetchSlots() {
    try {
        error.value = ''
        const url = showBookedOnly.value ? '/bookings/booked' : '/bookings'
        const res = await api.get(url)
        slots.value = Array.isArray(res.data) ? res.data : (res.data.data ?? [])
    } catch (e: any) {
        console.error('BOOKINGS ERROR:', e?.response?.status, e?.response?.data)
        error.value = 'Could not load time slots.'
    }
}

onMounted(async () => {
    await fetchSlots()
})

const filteredSlots = computed(() => {
    const q = search.value.trim().toLowerCase()
    if (!q) return slots.value

    return slots.value.filter((s) => {
        const start = `${s.time_slot_start ?? ''}`.toLowerCase()
        const end = `${s.time_slot_end ?? ''}`.toLowerCase()
        return start.includes(q) || end.includes(q)
    })
})

async function addTimeSlot() {
    try {
        error.value = ''

        const payload = {
            time_slot_start: toIsoStringFromDatetimeLocal(newSlot.value.start),
            time_slot_end: toIsoStringFromDatetimeLocal(newSlot.value.end)
        }

        if (!payload.time_slot_start || !payload.time_slot_end) {
            error.value = 'Start and end are required.'
            return
        }

        await api.post('/bookings', payload)

        newSlot.value.start = ''
        newSlot.value.end = ''
        await fetchSlots()
    } catch (e: any) {
        console.error('ADD TIMESLOT ERROR:', e?.response?.status, e?.response?.data)
        const status = e?.response?.status
        const msg = e?.response?.data?.message
        error.value = status ? `Could not create timeslot (${status})${msg ? ': ' + msg : ''}` : 'Could not create timeslot.'
    }
}

function startEdit(slot: TimeSlot) {
    const key = slot.booking_id ?? slot.id
    if (!key) return

    editingKey.value = key
    editSlot.value.start = (slot.time_slot_start ?? '').slice(0, 16)
    editSlot.value.end = (slot.time_slot_end ?? '').slice(0, 16)
}

function cancelEdit() {
    editingKey.value = null
    editSlot.value.start = ''
    editSlot.value.end = ''
}

async function updateTimeSlot(id: number | undefined) {
    if (!id) return

    try {
        error.value = ''

        const payload = {
            time_slot_start: toIsoStringFromDatetimeLocal(editSlot.value.start),
            time_slot_end: toIsoStringFromDatetimeLocal(editSlot.value.end)
        }

        if (!payload.time_slot_start || !payload.time_slot_end) {
            error.value = 'Start and end are required.'
            return
        }

        await api.put(`/admin/bookings/${id}`, payload)

        cancelEdit()
        await fetchSlots()
    } catch (e: any) {
        console.error('UPDATE TIMESLOT ERROR:', e?.response?.status, e?.response?.data)
        const status = e?.response?.status
        const msg = e?.response?.data?.message
        error.value = status ? `Could not update timeslot (${status})${msg ? ': ' + msg : ''}` : 'Could not update timeslot.'
    }
}

async function deleteTimeSlot(id: number | undefined) {
    if (!id) return

    try {
        error.value = ''
        await api.delete(`/admin/bookings/${id}`)
        await fetchSlots()
    } catch (e: any) {
        console.error('DELETE TIMESLOT ERROR:', e?.response?.status, e?.response?.data)
        error.value = 'Could not delete timeslot.'
    }
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

.nav-link:hover {
    text-decoration: underline;
}

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

.card {
    max-width: 900px;
    margin: 20px auto 40px;
    padding: 20px;
    border-radius: 8px;
    background: white;
    box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}

.create-section,
.search-section {
    margin-bottom: 15px;
}

.filter-row {
    margin-bottom: 12px;
}

.checkbox {
    color: #555;
    font-size: 14px;
}

input {
    padding: 8px;
    border-radius: 6px;
    border: 1px solid #ccc;
    margin-right: 8px;
    width: 60%;
}

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

.ghost-btn {
    background: transparent;
    color: #1976d2;
    border: none;
    padding: 0;
    font-weight: 500;
}

.ghost-btn:hover {
    text-decoration: underline;
    background: transparent;
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
