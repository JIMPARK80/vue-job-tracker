<template>
  <div class="app-container">
    <h1>Job Application Tracker</h1> <!-- Title of the page -->

    <button @click="toggleForm" class="new-button">
      {{ showForm ? 'Hide Form' : 'New Application' }}
    </button>

    <!-- Form to add a new application -->
    <form @submit.prevent="addApplication" v-if="showForm">
      <input v-model="form.company" placeholder="Company" required /> <!-- Company input field -->
      <input v-model="form.location" placeholder="Location" required /> <!-- Location  input field -->
      <input v-model="form.position" placeholder="Position" required /> <!-- Position input field -->
      <input v-model="form.type" placeholder="Employment Type" required /> <!-- Employment Type input field --> 
      <input v-model="form.date" type="date" required /> <!-- Date input field -->
      <input v-model="form.coverletter" placeholder="Cover Letter Info (Yes/No)" />
      <textarea v-model="form.notes" placeholder="Notes..." rows="2"></textarea> <!-- Notes input field -->
      <button type="submit">Add</button> <!-- Add button -->
    </form>

    <div v-if="applications.length">
      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>Company</th>
            <th>Location</th>
            <th>Position</th>
            <th>Type</th>
            <th>Date</th>
            <th>Cover Letter</th>
            <th>Notes</th>
            <th>Match Score (Updating)</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(app, index) in applications" :key="index">
            <td>{{ index + 1 }}</td>
            <!-- Company inline editable -->
            <td @click="startEdit(index,'company')">
              <template v-if="editing.row === index && editing.field === 'company'">
                <input v-model="app.company" @blur="stopEdit" @keyup.enter="stopEdit" class="inline-input" />
              </template>
              <template v-else>
                {{ app.company || '-' }}
              </template>
            </td>
            <!-- Location inline editable -->
            <td @click="startEdit(index,'location')">
              <template v-if="editing.row === index && editing.field === 'location'">
                <input v-model="app.location" @blur="stopEdit" @keyup.enter="stopEdit" class="inline-input" />
              </template>
              <template v-else>
                {{ app.location || '-' }}
              </template>
            </td>
            <!-- Position inline editable -->
             <td @click="startEdit(index, 'position')">
              <template v-if="editing.row === index && editing.field === 'position'">
                <input v-model="app.position" @blur="stopEdit" @keyup.enter="stopEdit" class="inline-input" />
              </template>
              <template v-else>
                {{ app.position || '-' }}
              </template>
             </td>
             
             <!-- Employment Type inline editable -->
             <td @click="startEdit(index, 'type')">
              <template v-if="editing.row === index && editing.field === 'type'">
                <input v-model="app.type" @blur="stopEdit" @keyup.enter="stopEdit" class="inline-input" />
              </template>
              <template v-else>
                {{ app.type || '-' }}
              </template>
            </td>

            <!-- Date inline editable -->
            <td @click="startEdit(index, 'date')">
              <template v-if="editing.row === index && editing.field === 'date'">
                <input v-model="app.date" @blur="stopEdit" @keyup.enter="stopEdit" class="inline-input" />
              </template>
              <template v-else>
                {{ app.date || '-' }}
              </template>
            </td>

            <!-- Cover Letter inline editable -->
            <td @click="startEdit(index, 'coverletter')">
              <template v-if="editing.row === index && editing.field == 'coverletter'">
                <input v-model="app.coverletter" @blur="stopEdit" @keyup.enter="stopEdit" class="inline-input" />
              </template>
              <template v-else>
                {{ app.coverletter || '-' }}
              </template>
            </td>  

            <!-- Notes: inline editable -->
            <td @click="startEdit(index, 'notes')">
              <template v-if="editing.row === index && editing.field === 'notes'">
                <input v-model="app.notes" @blur="stopEdit" @keyup.enter="stopEdit" class="inline-input" />
              </template>
              <template v-else>
                {{ app.notes || '-' }}
              </template>
            </td>

            <!-- Match Score: inline editable -->
             <td>
                {{ app.matchScore !== undefined && app.matchScore !== '' ? app.matchScore + '%' : '-' }}
             </td>

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
  location: '',
  type: '',
  coverletter: '',
  date: '',
  notes: '',
  matchScore: '',
})


const applications = ref([]) // Create a reactive reference to an empty array
const editing = ref({ row: null, field: null })
const showForm = ref(false) // Add this line to control form visibility

// when the page loads, check if there is any saved data in local storage
onMounted(() => {
  const saved = localStorage.getItem('applications')
  if (saved) {
      applications.value = JSON.parse(saved)
      let updated = false
      // Addtionaly, add a match score to the application
      applications.value.forEach(app => {
        if (app.matchScore === undefined || app.matchScore === ""){
          app.matchScore = Math.floor(Math.random() * 31) + 70 
          updated = true
        }
      })
      if (updated) {
        localStorage.setItem('applications', JSON.stringify(applications.value))
      }
  }
})

// if user add or remove an application, save the data to local storage (keep the data)
watch(applications, (newVal) => {
  localStorage.setItem('applications', JSON.stringify(newVal))
}, { deep: true })


// When user input data and click "Add" button, add the data to the applications array
function addApplication() {
  const randomScore = Math.floor(Math.random() * 31) + 70
  applications.value.push({ ...form.value, matchScore: randomScore })
  form.value = { 
    company: '', 
    position: '', 
    location: '',
    type: '',
    coverletter: '',
    date: '', 
    notes: '',
    matchScore: '',
  }
}

// When user remove an data from the list, remove the data
function removeApplication(index) {
  applications.value.splice(index, 1)
}

function startEdit(row, field){
  editing.value = { row, field }
}

function stopEdit() {
  editing.value = { row: null, field: null }
}

function toggleForm() {
  showForm.value = !showForm.value
}
</script>


<style>
/* minimum and clean default styles */
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;      /* horizontal center */
  justify-content: flex-start; /* vertical top */
  background: #01042c;
}

body {
  font-family: 'Segoe UI', sans-serif;
  padding: 40px;
  background-color: #01042c;
  color: rgb(0, 0, 0);
}

h1 {
  color: rgb(172, 243, 234);
  text-align: center;
  width: 100%;
  margin-bottom: 30px;
}

form {
  display: flex;
  flex-direction: column;
  gap: 12px;
  max-width: 600px;
  width: 100%;
  margin: 0 auto 40px auto;
}

input, textarea {
  padding: 15px;
  font-size: 16px;
  border: none;
  border-radius: 10px;
  background: #2e2e2e;
  color: white;
}

input::placeholder, textarea::placeholder {
  color: #aaaaaa;
}

button {
  background: black;
  color: white;
  font-weight: bold;
  border: 2px solid white;
  padding: 10px;
  border-radius: 6px;
  transition: 0.2s;
}

button:hover {
  background: white;
  color: black;
}

table {
  width: 100%;
  max-width: 900px;
  margin: 50px auto;
  border-collapse: collapse;
  background: rgb(201, 243, 255);
  box-shadow: 0 0 10px rgba(0,0,0,0.3);
  border-radius: 6px;
  overflow: hidden;
}

th, td {
  padding: 14px;
  font-size: 15px;
  text-align: center;
  border-left: 1px solid #2b2b2b;
  line-height: 1.7;
  vertical-align: top;
}

th {
  background-color: #444;
  color: white;
  min-width: 20px;
}

td button {
  background-color: #111;
  color: white;
  padding: 10px 10px;
  border-radius: 5px;
  font-size: 14px;
  border: none;
}

td button:hover {
  background-color: #f44336;
}

.inline-input {
  width: 100%;
  padding: 6px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
}

.new-button{
  background:rgb(172, 243, 234);
  border: none;
  color: rgb(0, 0, 0);
  font-weight: bold;
  border-radius: 6px;
  padding: 10px;
  font-size: 16px;
  cursor: pointer;
}

@media (max-width: 768px) {
  /* This part is applied only to mobile */
  table, thead, tbody, th, td, tr {
    display: block;
  }

  thead {
    display: none;
  }

  tr {
    margin-bottom: 20px;
    border: 1px solid #ccc;
    border-radius: 8px;
    background: #e1faff;
    padding: 10px;
  }

  td {
    position: relative;
    padding-left: 50%;
    text-align: left;
    border-bottom: none;
    font-size: 14px;
  }

  td::before {
    position: absolute;
    top: 50%;
    left: 10px;
    transform: translateY(-50%);
    font-weight: bold;
    color: #444;
    white-space: nowrap;
  }

  td:nth-of-type(1)::before { content: "#"; }
  td:nth-of-type(2)::before { content: "Company"; }
  td:nth-of-type(3)::before { content: "Location"; }
  td:nth-of-type(4)::before { content: "Position"; }
  td:nth-of-type(5)::before { content: "Employment Type"; }
  td:nth-of-type(6)::before { content: "Date"; }
  td:nth-of-type(7)::before { content: "Cover Letter"; }
  td:nth-of-type(8)::before { content: "Notes"; }
  td:nth-of-type(9)::before { content: "Action"; }
}



</style>

