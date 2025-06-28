<template>
  <form @submit.prevent="handleSubmit">
    <h2>{{ isLogin ? 'Login' : 'Register' }}</h2>
    <input v-model="email" placeholder="Email" type="email" required />
    <input v-model="password" placeholder="Password" type="password" required />
    <input
      v-if="!isLogin"
      v-model="name"
      placeholder="Full Name"
      type="text"
      
    />
    <input
      v-if="!isLogin"
      v-model="confirmPassword"
      placeholder="Confirm Password"
      type="password"
      required
    />
    <button type="submit">{{ isLogin ? 'Login' : 'Register' }}</button>
    <p>
      <a href="#" @click.prevent="toggleMode">
        {{ isLogin ? "Don't have an account? Register" : "Already have an account? Login" }}
      </a>
    </p>
    <p v-if="error" style="color: red;">{{ error }}</p>
  </form>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['login-success']);

const isLogin = ref(true);
const email = ref('');
const password = ref('');
const name = ref('');
const error = ref('');
const confirmPassword = ref('');

// Load registered users as objects: [{ email, password }]
const registeredUsers = ref(JSON.parse(localStorage.getItem('registeredUsers')) || []);

const handleSubmit = async () => {
  error.value = '';
  if (!isLogin.value && password.value !== confirmPassword.value) {
    error.value = 'Passwords do not match.';
    return;
  }
  if (password.value.length < 6) {
    error.value = 'Password must be at least 6 characters long.';
    return;
  }
  try {
    if (isLogin.value) {
      // Find user by email
      const user = registeredUsers.value.find(u => u.email === email.value);
      if (!user) {
        error.value = 'Email is not registered. Please register first.';
        return;
      }
      if (user.password !== password.value) {
        error.value = 'Please enter a valid password.';
        return;
      }
      emit('login-success');
    } else {
      // Registration: check if email already exists
      if (registeredUsers.value.some(u => u.email === email.value)) {
        error.value = 'Username (email) already taken.';
        return;
      }
      // Add new user as object
      registeredUsers.value.push({ email: email.value, password: password.value });
      localStorage.setItem('registeredUsers', JSON.stringify(registeredUsers.value));
      emit('login-success');
    }
  } catch (e) {
    error.value = 'Authentication failed. Please try again.';
  }
};

const toggleMode = () => {
  isLogin.value = !isLogin.value;
  error.value = '';
};
</script>