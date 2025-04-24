<template>
  <div>
    <h1>Job Application Tracker</h1> <!-- Title of the page -->

    <!-- Form to add a new application -->
    <form @submit.prevent="addApplication"> 
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
            <th>Employment Type</th>
            <th>Date</th>
            <th>Cover Letter</th>
            <th>Notes</th>
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
  notes: ''
})


const applications = ref([]) // Create a reactive reference to an empty array
const editing = ref({ row: null, field: null })

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
  form.value = { 
    company: '', 
    position: '', 
    location: '',
    type: '',
    coverletter: '',
    date: '', 
    notes: '' 
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
</script>


<style>
/* minimum and clean default styles */


body {
  font-family: 'Segoe UI', sans-serif;
  padding: 40px;
  background-color: #01042c;
  color: rgb(0, 0, 0);
}

h1 {
  color: rgb(172, 243, 234);
  text-align: center;
  margin-bottom: 30px;
}

form {
  display: flex;
  flex-direction: column;
  gap: 12px;
  max-width: 600px;
  margin: 0 auto 40px auto;
}

input, textarea {
  padding: 12px;
  font-size: 16px;
  border: none;
  border-radius: 6px;
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
  margin: 0 auto;
  border-collapse: collapse;
  background: rgb(201, 243, 255);
  box-shadow: 0 0 10px rgba(0,0,0,0.3);
  border-radius: 6px;
  overflow: hidden;
}

th, td {
  padding: 10px;
  font-size: 18px;
  text-align: left;
  font-weight: bold;
  border-bottom: 2px solid #000;
}

th {
  background-color: #444;
  color: white;
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
</style>

