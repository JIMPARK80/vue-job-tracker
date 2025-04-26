<template>
  <div class="app-container">
    <h1>Job Application Tracker</h1>

    <button @click="toggleForm" class="new-button">
      {{ showForm ? 'Hide Form' : 'New Application' }}
    </button>

    <!-- Form -->
    <form @submit.prevent="addApplication" v-if="showForm" class="application-form">
      <input v-model="form.company" placeholder="Company" required />
      <input v-model="form.location" placeholder="Location" required />
      <input v-model="form.position" placeholder="Position" required />
      <input v-model="form.type" placeholder="Employment Type" required />
      <input v-model="form.date" type="date" required />
      <input v-model="form.coverletter" placeholder="Cover Letter Info (Yes/No)" />
      <textarea v-model="form.notes" placeholder="Notes..." rows="2"></textarea>
      <button type="submit">Add</button>
    </form>

    <!-- Cards -->
    <div v-if="applications.length" class="cards-container">
      <div v-for="(app, index) in applications" :key="index" class="card">
        <p><strong>#{{ index + 1 }}</strong></p>
        <p><strong>Company:</strong> {{ app.company || '-' }}</p>
        <p><strong>Location:</strong> {{ app.location || '-' }}</p>
        <p><strong>Position:</strong> {{ app.position || '-' }}</p>
        <p><strong>Type:</strong> {{ app.type || '-' }}</p>
        <p><strong>Date:</strong> {{ app.date || '-' }}</p>
        <p><strong>Cover Letter:</strong> {{ app.coverletter || '-' }}</p>
        <p><strong>Notes:</strong> {{ app.notes || '-' }}</p>
        <p><strong>Match Score:</strong> {{ app.matchScore !== undefined && app.matchScore !== '' ? app.matchScore + '%' : '-' }}</p>
        <button @click="removeApplication(index)">Delete</button>
      </div>
    </div>

    <div v-else>
      <p>No applications yet.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'

const form = ref({
  company: '', position: '', location: '', type: '', coverletter: '', date: '', notes: '', matchScore: ''
})
const applications = ref([])
const editing = ref({ row: null, field: null })
const showForm = ref(false)

onMounted(() => {
  const saved = localStorage.getItem('applications')
  if (saved) {
    applications.value = JSON.parse(saved)
    let updated = false
    applications.value.forEach(app => {
      if (app.matchScore === undefined || app.matchScore === '') {
        app.matchScore = Math.floor(Math.random() * 31) + 70
        updated = true
      }
    })
    if (updated) {
      localStorage.setItem('applications', JSON.stringify(applications.value))
    }
  }
})

watch(applications, (newVal) => {
  localStorage.setItem('applications', JSON.stringify(newVal))
}, { deep: true })

function addApplication() {
  const randomScore = Math.floor(Math.random() * 31) + 70
  applications.value.push({ ...form.value, matchScore: randomScore })
  form.value = { company: '', position: '', location: '', type: '', coverletter: '', date: '', notes: '', matchScore: '' }
}

function removeApplication(index) {
  applications.value.splice(index, 1)
}

function toggleForm() {
  showForm.value = !showForm.value
}
</script>


<style>
.app-container {
  min-height: 100vh;
  padding: 40px;
  background: #01042c;
  color: white;
  display: flex;
  flex-direction: column;
  align-items: center;
}

h1 {
  color: rgb(172, 243, 234);
  margin-bottom: 20px;
}

.new-button {
  background: rgb(172, 243, 234);
  color: black;
  font-weight: bold;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  margin-bottom: 30px;
  cursor: pointer;
}

.application-form {
  display: flex;
  flex-direction: column;
  gap: 12px;
  width: 100%;
  max-width: 400px;
  margin-bottom: 40px;
}

.application-form input,
.application-form textarea {
  padding: 12px;
  border-radius: 8px;
  border: none;
  background: #2e2e2e;
  color: white;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  width: 100%;
}

.card {
  background: #e1faff;
  color: black;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  width: 280px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  text-align: left;
  transition: 0.3s;
}

.card:hover {
  transform: translateY(-5px);
}

.card button {
  align-self: flex-end;
  margin-top: auto;
  background: #111;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 6px;
  cursor: pointer;
}

.card button:hover {
  background: #f44336;
}
</style>


