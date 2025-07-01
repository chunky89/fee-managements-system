<template>
  <div id="app">
    <!-- Logo and Name Header -->
    <div v-if="loggedIn" class="header">
      <img src="/src/assets/logo.svg" alt="Logo" class="app-logo" />
      <div class="app-title-details">
        <h1>Fee Management System</h1>
        <div class="user-details">
          <span class="user-name">{{ user.name }}</span>
          <span class="user-email">{{ user.email }}</span>
        </div>
      </div>
    </div>
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
          <Payment @order-completed="onOrderCompleted" />
        </div>
        <!-- Pending Orders Section -->
        <div class="card pending-orders">
          <div class="header">
            <img src="/src/assets/logo.svg" alt="Logo" class="app-logo" />
            <div class="app-title-details">
              <h2>Pending Orders</h2>
              <div class="user-details">
                <span class="user-name">{{ user.name }}</span>
                <span class="user-email">{{ user.email }}</span>
              </div>
            </div>
          </div>
          <div class="pending-orders-list">
            <p>No pending orders at the moment.</p>
            <!-- You can replace this with your actual pending orders logic -->
          </div>
        </div>
        <div class="card">
          <h2>Student Management</h2>
          <p v-if="!students.length">No students added yet.</p>
          <p v-else>Manage students and their fees below:</p>
          <p v-if="students.length">Total Students: {{ students.length }}</p>
          <p v-if="totalCollected > 0">Total Fees Collected: {{ totalCollected }}</p>
          <p v-if="totalCollected === 0">No fees collected yet.</p>

          <!-- Confirmation message after order completion -->
          <div v-if="orderCompleted" class="confirmation-message">
            <h3>Thank you! Your order has been completed successfully.</h3>
            <p>A confirmation email has been sent to {{ user.email }}.</p>
          </div>

          <!-- Your main app UI (student table, etc.) --> 
          <form @submit.prevent="addStudent">
            <input v-model="newStudent.name" placeholder="Student Name" required />
            <input v-model.number="newStudent.fee" type="number" placeholder="Fee Amount" required />
            <input v-model="newStudent.dueDate" type="date" placeholder="Due Date" required />
            <button type="submit">Add Student</button>
          </form>
          <table>
            <thead>
              <tr>
                <th>Name</th>
                <th>Fee</th>
                <th>Due Date</th>
                <th>Status</th>
                <th>Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(student, index) in students" :key="index">
                <td>{{ student.name }}</td>
                <td>{{ student.fee }}</td>
                <td>{{ student.dueDate }}</td>
                <td>
                  <span v-if="student.paid" style="color: green;">Paid</span>
                  <span v-else-if="isOverdue(student.dueDate)" style="color: orange;">Overdue</span>
                  <span v-else style="color: red;">Unpaid</span>
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
      user: {
        name: 'John Doe', // Placeholder, should be set after login
        email: 'john.doe@email.com', // Placeholder, should be set after login
      },
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
        dueDate: ""
      },
      students: [],
      orderCompleted: false,
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
    onLoginSuccess(userDetails) {
      this.loggedIn = true;
      if (userDetails) {
        this.user = userDetails;
      }
    },
    logout() {
      this.loggedIn = false;
      this.orderCompleted = false;
    },
    addStudent() {
      if (!this.newStudent.name || this.newStudent.fee <= 0 || !this.newStudent.dueDate) return;
      this.students.push({ ...this.newStudent });
      this.newStudent.name = "";
      this.newStudent.fee = 0;
      this.newStudent.paid = false;
      this.newStudent.dueDate = "";
    },
    togglePaid(index) {
      this.students[index].paid = !this.students[index].paid;
    },
    removeStudent(index) {
      this.students.splice(index, 1);
    },
    onCourseSelected(courseId) {
      this.selectedCourse = courseId;
    },
    onOrderCompleted() {
      this.orderCompleted = true;
      // Optionally, you can reset other states or trigger additional logic here
    },
    isOverdue(dueDate) {
      if (!dueDate) return false;
      const today = new Date();
      const due = new Date(dueDate);
      // Set time to 0 for accurate date comparison
      today.setHours(0,0,0,0);
      due.setHours(0,0,0,0);
      return due < today;
    }
  },
};
</script>

<style scoped>
.header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 20px;
}
.app-logo {
  width: 48px;
  height: 48px;
}
.app-title-details {
  display: flex;
  flex-direction: column;
}
.user-details {
  font-size: 0.95rem;
  color: #555;
}
.user-name {
  font-weight: bold;
  margin-right: 10px;
}
.user-email {
  font-style: italic;
}
.confirmation-message {
  background: #e6ffe6;
  border: 1px solid #b2ffb2;
  padding: 16px;
  margin-bottom: 20px;
  border-radius: 8px;
  color: #2d7a2d;
  text-align: center;
}

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
.pending-orders {
  margin-top: 20px;
}
.pending-orders .header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 16px;
}
.pending-orders-list {
  background: #f9f9f9;
  padding: 16px;
  border-radius: 8px;
  border: 1px solid #ddd;
}

.app-logo {
  width: 32px;
  height: 32px;
}
</style>