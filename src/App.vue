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
import { ref, watch, onMounted } from 'vue' // Import Vue core functions
// ref: create a reactive reference to a value.
// watch: track changes to a value.
// onMounted: when the page loads, run this function.

// Define a form object with fields for company, position, date, score, and notes
const form = ref({
  // Initialize form fields with empty values
  company: '',
  position: '',
  date: '',
  score: 0,
  notes: ''
})


const applications = ref([]) 

// when the page loads, check if there is any saved data in local storage
onMounted(() => {
  const saved = localStorage.getItem('applications')
  if (saved) applications.value = JSON.parse(saved)
})

// if user add or remove an application, save the data to local storage (keep the data)
watch(applications, (newVal) => {
  localStorage.setItem('applications', JSON.stringify(newVal))
}, { deep: true })


// When user input data and click "Add" button, add the data to the applications array
function addApplication() {
  applications.value.push({ ...form.value })
  form.value = { company: '', position: '', date: '', score: 0, notes: '' }
}

// When user remove an data from the list, remove the data
function removeApplication(index) {
  applications.value.splice(index, 1)
}
</script>


<style>
/* minimum and clean default styles */

body{
  font-family: sans-serif; /* font family */
  padding: 20px; /* padding */
  background: #18a7b2; /* background color */
}

input, textarea, button{
  margin: 20px 0ch; /* margin */
  width: 80%; /* width */
  padding: 10px; /* thickenss box */
  font-size: 15px; /* font size */
}

button{
  cursor: pointer; /* cursor */
}


</style>