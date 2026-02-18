<template>
  <div class="page">
    <h1>Treatment Records</h1>

    <div class="create-box">
      <input v-model="newTreatment" placeholder="New treatment description" />
      <button @click="addTreatment">Add</button>
    </div>

    <ul>
      <li v-for="treatment in treatments" :key="treatment.id">
        {{ treatment.description }}
        <button @click="deleteTreatment(treatment.id)">Delete</button>
      </li>
    </ul>

    <div class="back">
      <router-link :to="`/doctor/patients/${patientId}`">
        Back to Patient
      </router-link>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const patientId = route.params.id

interface Treatment {
  id: number
  description: string
}

const treatments = ref<Treatment[]>([
  { id: 1, description: 'Initial diagnosis' },
  { id: 2, description: 'Follow-up visit' },
])

const newTreatment = ref('')

function addTreatment() {
  if (!newTreatment.value) return

  treatments.value.push({
    id: Date.now(),
    description: newTreatment.value,
  })

  newTreatment.value = ''
}

function deleteTreatment(id: number) {
  treatments.value = treatments.value.filter(t => t.id !== id)
}
</script>

<style scoped>
.page {
  max-width: 800px;
  margin: 30px auto;
}

.create-box {
  margin: 15px 0;
}

input {
  padding: 6px;
  margin-right: 8px;
}

button {
  padding: 6px 10px;
  margin-left: 5px;
  cursor: pointer;
}

ul {
  margin-top: 15px;
}

li {
  margin-bottom: 8px;
}

.back {
  margin-top: 20px;
}
</style>

