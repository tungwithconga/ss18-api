<template>
    <div>
      <h1>Student Detail</h1>
      <div v-if="student">
        <p><strong>ID:</strong> {{ student.id }}</p>
        <p><strong>Name:</strong> {{ student.student_name }}</p>
        <p><strong>Email:</strong> {{ student.email }}</p>
        <p><strong>Address:</strong> {{ student.address }}</p>
        <p><strong>Phone:</strong> {{ student.phone }}</p>
        <p><strong>Status:</strong> {{ student.status ? 'Active' : 'Inactive' }}</p>
        <p><strong>Created At:</strong> {{ student.created_at }}</p>
      </div>
      <p v-else>{{ message }}</p>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  
  const student = ref(null);
  const message = ref('');
  
  const getStudentById = async (id) => {
    try {
      const response = await axios.get(`http://localhost:3000/students/${id}`);
      if (response.data) {
        console.log(response.data);
        student.value = response.data;
      } else {
        message.value = 'Không tìm thấy bản ghi';
      }
    } catch (error) {
      console.error('Error fetching student:', error);
      message.value = 'Không tìm thấy bản ghi';
    }
  };
  
  onMounted(() => {
    getStudentById(1);
  });
  </script>
  