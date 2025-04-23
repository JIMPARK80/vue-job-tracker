<template>
  <div>
    <h1>Job Application Tracker</h1> <!-- Title of the page -->

    <!-- Form to add a new application -->
    <form @submit.prevent="addApplication"> 
      <input v-model="form.company" placeholder="Company" required /> <!-- Company input field -->
      <input v-model="form.position" placeholder="Position" required /> <!-- Position input field -->
      <input v-model="form.date" type="date" required /> <!-- Date input field -->
      <input v-model.number="form.score" type="number" placeholder="Score %" required min="0" max="100" /> 
      <!-- Score input field -->
      <textarea v-model="form.notes" placeholder="Notes..." rows="2"></textarea> <!-- Notes input field -->
      <button type="submit">Add</button> <!-- Add button -->
    </form>


    <div v-if="applications.length">
      <div v-for="(app, index) in applications" :key="index">
        <strong>{{ app.company }}</strong> – {{ app.position }}<br />
        Date: {{ app.date }} | Score: {{ app.score }}%<br />
        <em>{{ app.notes }}</em><br />
        <button @click="removeApplication(index)">Delete</button>
      </div>
    </div>

    <!-- If there are no applications, show this message -->
    <div v-else>
      <p>No applications yet.</p>
    </div>


  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'

const form = ref({
  company: '',
  position: '',
  date: '',
  score: 0,
  notes: ''
})

const applications = ref([])

onMounted(() => {
  const saved = localStorage.getItem('applications')
  if (saved) applications.value = JSON.parse(saved)
})

watch(applications, (newVal) => {
  localStorage.setItem('applications', JSON.stringify(newVal))
}, { deep: true })

function addApplication() {
  applications.value.push({ ...form.value })
  form.value = { company: '', position: '', date: '', score: 0, notes: '' }
}

function removeApplication(index) {
  applications.value.splice(index, 1)
}
</script>

<!-- Style removed for learning HTML + JS logic first -->
<style scoped>
/* Styles temporarily removed for practicing raw Vue structure */
</style>