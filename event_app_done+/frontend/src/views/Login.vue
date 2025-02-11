<template>
    <div class="max-w-md mx-auto p-6 bg-white rounded-lg shadow-md">
      <form @submit.prevent="handleSubmit" class="space-y-4">
        <div>
          <label for="username" class="block text-sm font-medium text-gray-700">Username</label>
          <input 
            class="field border border-gray-300 rounded-md p-2 w-full focus:ring focus:ring-blue-200 focus:border-blue-500" 
            name="username" 
            type="text" 
            v-model="username" 
            required
          />
        </div>
        <div>
          <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
          <input 
            class="field border border-gray-300 rounded-md p-2 w-full focus:ring focus:ring-blue-200 focus:border-blue-500" 
            name="email" 
            type="email" 
            v-model="email" 
            required
          />
        </div>
        <div>
          <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
          <input 
            class="field border border-gray-300 rounded-md p-2 w-full focus:ring focus:ring-blue-200 focus:border-blue-500" 
            name="password" 
            type="password" 
            v-model="password" 
            required
          />
        </div>
        <div>
          <label for="confirm_password" class="block text-sm font-medium text-gray-700">Confirm Password</label>
          <input 
            class="field border border-gray-300 rounded-md p-2 w-full focus:ring focus:ring-blue-200 focus:border-blue-500" 
            name="confirm_password" 
            type="password" 
            v-model="confirmPassword" 
            required
          />
        </div>
        <button 
          type="submit" 
          class="w-full bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition duration-200"
        >
          Submit
        </button>
      </form>
  
      <div v-if="messages.length !== 0" class="mt-6">
        <ul class="space-y-2">
          <li v-for="(message, index) in messages" :key="index" class="text-gray-600">
            {{ message }}
          </li>
        </ul>
      </div>
    </div>
  </template>
  

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import Cookie from "js-cookie";

const username = ref('');
const email = ref('');
const password = ref('');
const confirmPassword = ref('');
const router = useRouter();
const messages = ref([])

async function handleSubmit(e) {
    e.preventDefault();

    const usernameRegex = /^[a-zA-Z0-9]{3,20}$/;
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    const passwordRegex = /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/;

    if (!username.value) {
        alert("Username field should be not empty")
        // alert('Invalid username. It should be 3-20 alphanumeric characters.');
        return;
    }
    if (!email.value) {
        alert("Email field should be not empty")
        // alert('Invalid email format.');
        return;
    }
    if (!password.value) {
        alert("Password field should be not empty")
        // alert('Password must be at least 8 characters long and contain at least one letter and one number.');
        return;
    }
    if (password.value !== confirmPassword.value) {
        alert('Passwords do not match.');
        return;
    }

    const userData = {
        username: username.value,
        email: email.value,
        password: password.value,
    };

    fetchUserData(userData)
}

const fetchUserData = async (userData) => {
    try {
        const res = await fetch('http://localhost:5000/auth/login', {
        method: 'POST',
        headers: {
        'Content-Type': 'application/json',
    },
        body: JSON.stringify(userData),
    })
    if (!res.ok) {
        throw new Error('Network response was not ok, possibly invalid credentials');
    }
        const data = await res.json();
        localStorage.setItem('jwt', data.token);
        if (data.message) {
            messages.value.push(data.message)
        }
        Cookie.set("userId", data.userId);
        router.push('/');
    } catch (error) {
        console.error('There was a problem with the fetch operation', error);
        messages.value.push(error)
    }
}

</script>

<style>
.field {
    margin: 10px 0;
    padding: 10px;
    border: 3px solid black;
    border-radius: 5px;
}
</style>