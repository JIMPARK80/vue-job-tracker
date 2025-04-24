<template>
  <div>
    <h1>Job Application Tracker</h1> <!-- Title of the page -->

    <!-- Form to add a new application -->
    <form @submit.prevent="addApplication"> 
      <input v-model="form.company" placeholder="Company" required /> <!-- Company input field -->
      <input v-model="form.position" placeholder="Position" required /> <!-- Position input field -->
      <input v-model="form.date" type="date" required /> <!-- Date input field -->
      <textarea v-model="form.notes" placeholder="Notes..." rows="2"></textarea> <!-- Notes input field -->
      <button type="submit">Add</button> <!-- Add button -->
    </form>

    <div v-if="applications.length">
      <table>
        <thead>
          <tr>
            <th>Company</th>
            <th>Position</th>
            <th>Date</th>
            <th>Notes</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(app, index) in applications" :key="index">
            <td>{{ app.company }}</td>
            <td>{{ app.position }}</td>
            <td>{{ app.date }}</td>
            <td>{{ app.notes }}</td>
            <td>
              <button @click="removeApplication(index)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
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

// Define a form object with fields for company, position, date, and notes
const form = ref({
  // Initialize form fields with empty values
  company: '',
  position: '',
  date: '',
  notes: ''
})


const applications = ref([]) // Create a reactive reference to an empty array

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
  form.value = { company: '', position: '', date: '', notes: '' }
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
  background: #01042c; /* background color */
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
}

input, textarea, button{
  margin: 20px 0ch; /* margin */
  width: 100%; /* width */
  padding: 10px; /* thickenss box */
  font-size: 20px; /* font size */
}

button{
  cursor: pointer; /* cursor */
}

table {
  width: 100%;
  border-collapse: collapse;
  background: rgb(45, 184, 202);
  box-shadow: 0 0 5px rgba(0,0,0,0.2);
}

th, td {
  font-size: 20px;
  color:black;
  padding: 10px;
  border: 1px solid #000000;
  text-align: left;
}

th {
  background-color: #5e5e5e;
}

</style>