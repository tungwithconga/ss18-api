<template>
    <div>
      <h1>Student List</h1>
  
      <!-- Danh sách sinh viên -->
      <ul>
        <li v-for="student in students" :key="student.id">
          {{ student.name }} - {{ student.email }} - {{ student.address }}
          <button @click="removeById(student.id)">Remove</button>
          <button @click="editStudent(student)">Edit</button>
        </li>
      </ul>
  
      <!-- Form thêm mới sinh viên -->
      <h3>Create New Student</h3>
      <form @submit.prevent="createStudent" v-if="!isEditing">
        <input v-model="newStudent.name" placeholder="Student Name" />
        <input v-model="newStudent.email" placeholder="Email" />
        <input v-model="newStudent.address" placeholder="Address" />
        <input v-model="newStudent.phone" placeholder="Phone" />
        <input type="checkbox" v-model="newStudent.status" /> Active
        <button type="submit">Create Student</button>
      </form>
  
      <!-- Form cập nhật sinh viên -->
      <h3 v-if="isEditing">Update Student</h3>
      <form @submit.prevent="updateStudentById" v-if="isEditing">
        <input v-model="editStudentData.name" placeholder="Student Name" />
        <input v-model="editStudentData.email" placeholder="Email" />
        <input v-model="editStudentData.address" placeholder="Address" />
        <input v-model="editStudentData.phone" placeholder="Phone" />
        <input type="checkbox" v-model="editStudentData.status" /> Active
        <button type="submit">Update Student</button>
        <button @click="resetForm">Cancel</button>
      </form>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  
  // Biến lưu danh sách sinh viên
  const students = ref([]);
  
  // Biến lưu trạng thái tạo và chỉnh sửa
  const isEditing = ref(false);
  
  // Đối tượng sinh viên mới để thêm
  const newStudent = ref({
    name: '',
    email: '',
    address: '',
    phone: '',
    status: false,
    created_at: new Date().toISOString(),
  });
  
  // Đối tượng sinh viên cần chỉnh sửa
  const editStudentData = ref({
    id: null,
    name: '',
    email: '',
    address: '',
    phone: '',
    status: false,
    created_at: new Date().toISOString(),
  });
  
  // Hàm lấy danh sách sinh viên
  const getAllStudent = async () => {
    try {
      const response = await axios.get('http://localhost:8080/products');
      return response.data;
    } catch (error) {
      console.error('Error fetching students:', error);
      return [];
    }
  };
  
  // Hàm xóa sinh viên theo ID
  const removeById = async (id) => {
    try {
      const response = await axios.delete(`http://localhost:8080/products/${id}`);
      console.log('Deleted student:', response.data);
      students.value = students.value.filter(student => student.id !== id);
    } catch (error) {
      console.error('Error deleting student:', error);
    }
  };
  
  // Hàm thêm sinh viên mới
  const createStudent = async () => {
    try {
      const response = await axios.post('http://localhost:8080/products', newStudent.value);
      console.log('Created student:', response.data);
      students.value.push(response.data); // Thêm sinh viên vào danh sách
      resetForm(); // Reset form sau khi thêm
    } catch (error) {
      console.error('Error creating student:', error);
    }
  };
  
  // Hàm cập nhật sinh viên theo ID
  const updateStudentById = async () => {
    if (editStudentData.value.id) {
      try {
        const response = await axios.put(
          `http://localhost:8080/products/${editStudentData.value.id}`, 
          editStudentData.value
        );
        console.log('Updated student:', response.data);
        students.value = students.value.map(student => 
          student.id === editStudentData.value.id ? response.data : student
        );
        resetForm(); // Reset form sau khi cập nhật
      } catch (error) {
        console.error('Error updating student:', error);
      }
    }
  };
  
  // Hàm thiết lập dữ liệu sinh viên cần chỉnh sửa
  const editStudent = (student) => {
    editStudentData.value = { ...student };
    isEditing.value = true;
  };
  
  // Hàm reset form sau khi thêm hoặc chỉnh sửa xong
  const resetForm = () => {
    newStudent.value = {
      name: '',
      email: '',
      address: '',
      phone: '',
      status: false,
      created_at: new Date().toISOString(),
    };
    editStudentData.value = {
      id: null,
      name: '',
      email: '',
      address: '',
      phone: '',
      status: false,
      created_at: new Date().toISOString(),
    };
    isEditing.value = false;
  };
  
  onMounted(async () => {
    const studentList = await getAllStudent();
    students.value = studentList;
  });
  </script>
  