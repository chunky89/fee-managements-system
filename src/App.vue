<template>
  <div id="app">
    <h1>Fee Management System</h1>
    <button
      v-if="loggedIn"
      @click="logout"
      class="logout-btn"
    >
      Logout
    </button>
    <AuthForm v-if="!loggedIn" @login-success="onLoginSuccess" />
    <div v-else>
   <div class="card-container">
        <div class="card">
          <CourseSelection
            :courses="courses"
            @course-selected="onCourseSelected"
          />
        </div>
        <div class="card" v-if="selectedCourse">
          <VoucherGenerator />
        </div>
        <div class="card">
          <Payment />
        </div>
      </div>
      <h2>Student Management</h2>
      <p v-if="!students.length">No students added yet.</p>
      <p v-else>Manage students and their fees below:</p>
      <p v-if="students.length">Total Students: {{ students.length }}</p>
      <p v-if="totalCollected > 0">Total Fees Collected: {{ totalCollected }}</p>
      <p v-if="totalCollected === 0">No fees collected yet.</p>
    
      <!-- Your main app UI (student table, etc.) --> 
      <form @submit.prevent="addStudent">
        <input v-model="newStudent.name" placeholder="Student Name" required />
        <input v-model.number="newStudent.fee" type="number" placeholder="Fee Amount" required />
        <button type="submit">Add Student</button>
      </form>
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Fee</th>
            <th>Status</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(student, index) in students" :key="index">
            <td>{{ student.name }}</td>
            <td>{{ student.fee }}</td>
            <td>
              <span :style="{ color: student.paid ? 'green' : 'red' }">
                {{ student.paid ? 'Paid' : 'Unpaid' }}
              </span>
            </td>
            <td>
              <button @click="togglePaid(index)">
                {{ student.paid ? 'Mark Unpaid' : 'Mark Paid' }}
              </button>
              <button @click="removeStudent(index)">Remove</button>
            </td>
          </tr>
        </tbody>
      </table>
      <p>Total Collected: {{ totalCollected }}</p>
    </div>
  </div>
</template>

<script>
import AuthForm from './components/AuthForm.vue';
import VoucherGenerator from './components/VoucherGenerator.vue';
import CourseSelection from './components/CourseSelection.vue';
import jsPDF from 'jspdf';
import Payment from './components/Payment.vue';


export default {
  name: "App",
  components: { AuthForm, VoucherGenerator, CourseSelection, Payment },
  data() {
    return {
      loggedIn: false,
      courses: [
        { id: 1, name: 'Mathematics' },
        { id: 2, name: 'Computer Science' },
        { id: 3, name: 'English' },
        { id: 4, name: 'Pyscology' },
        { id: 5, name: 'Science' },
        { id: 6, name: 'History' },
        { id: 7, name: 'Geography' },
        { id: 8, name: 'Economics' },
        { id: 9, name: 'Physics' },
        { id: 10, name: 'Chemistry' }
      ],
      selectedCourse: null,
      newStudent: {
        name: "",
        fee: 0,
        paid: false,
      },
      students: [],
    };
  },
  computed: {
    totalCollected() {
      return this.students
        .filter((s) => s.paid)
        .reduce((sum, s) => sum + s.fee, 0);
    },
  },
  methods: {
    onLoginSuccess() {
      this.loggedIn = true;
    },
    logout() {
      this.loggedIn = false;
    },
    addStudent() {
      if (!this.newStudent.name || this.newStudent.fee <= 0) return;
      this.students.push({ ...this.newStudent });
      this.newStudent.name = "";
      this.newStudent.fee = 0;
      this.newStudent.paid = false;
    },
    togglePaid(index) {
      this.students[index].paid = !this.students[index].paid;
    },
    removeStudent(index) {
      this.students.splice(index, 1);
    },
    onCourseSelected(courseId) {
      this.selectedCourse = courseId;
    }
  },
};
</script>

<style scoped>

body {
  margin: 0;
}

#app {
  max-width: 600px;
  margin: 40px auto;
  font-family: Arial, sans-serif;
  position: relative;
  padding-top: 60px; /* Add some space at the top */
}
h1 {
  text-align: center;
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 2rem;
  font-weight: bold;
  letter-spacing: 1px;
}

h2{
  text-align: center;
  margin-top: 20px;
  font-size: 1.5rem;
  color: #333;
}

form {
  margin-bottom: 20px;
}
.card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  align-items: flex-start;
}
.logout-btn {
  position: fixed;
  top: 20px;
  right: 20px;
  font-size: 0.9rem;
  padding: 5px 10px;
  background-color: grey;
  color: #fff;
  border: none;
  border-radius: 15px;
  cursor: pointer;
}

.logout-btn:hover {
  background-color: red;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 10px;
}
th, td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: left;
}
button {
  margin-right: 5px;
}
</style>