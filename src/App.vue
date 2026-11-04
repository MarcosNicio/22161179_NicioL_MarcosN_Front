<template>
  <div class="container">
    <h1>Panel de Administración y Creación</h1>

    <div v-if="error" class="error-msg">{{ error }}</div>
    <div v-if="success" class="success-msg">{{ success }}</div>

    <!-- Pestañas de Navegación -->
    <div class="tabs">
      <button @click="seccionActual = 'roles'" :class="{ active: seccionActual === 'roles' }" class="btn-tab">Roles</button>
      <button @click="seccionActual = 'users'" :class="{ active: seccionActual === 'users' }" class="btn-tab">Usuarios</button>
      <button @click="seccionActual = 'employees'" :class="{ active: seccionActual === 'employees' }" class="btn-tab">Empleados</button>
    </div>

    <!-- ================= SECCIÓN ROLES ================= -->
    <div v-if="seccionActual === 'roles'">
      <div class="form-card">
        <h2>Nuevo Rol</h2>
        <form @submit.prevent="guardarRol">
          <input type="text" v-model="formRol.name" placeholder="Nombre (ej. admin, manager, cashier)" required class="input-field" />
          <input type="text" v-model="formRol.description" placeholder="Descripción" class="input-field" />
          <button type="submit" class="btn btn-save">Crear Rol</button>
        </form>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr><th>ID</th><th>Nombre</th><th>Descripción</th></tr>
          </thead>
          <tbody>
            <tr v-for="rol in roles" :key="rol.id">
              <td>{{ rol.id }}</td>
              <td><strong>{{ rol.name }}</strong></td>
              <td>{{ rol.description || 'Sin descripción' }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- ================= SECCIÓN USUARIOS ================= -->
    <div v-if="seccionActual === 'users'">
      <div class="form-card">
        <h2>Registrar Nuevo Usuario</h2>
        <form @submit.prevent="guardarUsuario">
          <input type="text" v-model="formUser.username" placeholder="Username (ej. anita99)" required class="input-field" />
          <input type="email" v-model="formUser.email" placeholder="Correo electrónico" required class="input-field" />
          <input type="password" v-model="formUser.password_hash" placeholder="Contraseña (password_hash)" required class="input-field" />
          
          <!-- Selector dinámico para el role_id basado en tus roles de BD -->
          <label style="font-size: 14px; color: #9ca3af; display: block; margin-bottom: 5px;">Rol del Usuario:</label>
          <select v-model="formUser.role_id" required class="input-field">
            <option disabled value="">-- Selecciona un rol --</option>
            <option v-for="rol in roles" :key="rol.id" :value="rol.id">
              ID: {{ rol.id }} - {{ rol.name }}
            </option>
          </select>

          <button type="submit" class="btn btn-save">Crear Usuario</button>
        </form>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr><th>ID</th><th>Username</th><th>Email</th><th>Role ID</th><th>Activo</th></tr>
          </thead>
          <tbody>
            <tr v-for="user in users" :key="user.id">
              <td>{{ user.id }}</td>
              <td><strong>{{ user.username }}</strong></td>
              <td>{{ user.email }}</td>
              <td>{{ user.role_id }}</td>
              <td>{{ user.is_active ? 'Sí' : 'No' }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- ================= SECCIÓN EMPLEADOS ================= -->
    <div v-if="seccionActual === 'employees'">
      <div class="form-card">
        <h2>Registrar Nuevo Empleado</h2>
        <form @submit.prevent="guardarEmpleado">
          <label style="font-size: 14px; color: #9ca3af; display: block; margin-bottom: 5px;">Usuario Asociado (user_id):</label>
          <select v-model="formEmp.user_id" required class="input-field">
            <option disabled value="">-- Selecciona un usuario existente --</option>
            <option v-for="user in users" :key="user.id" :value="user.id">
              ID: {{ user.id }} - {{ user.username }}
            </option>
          </select>

          <input type="text" v-model="formEmp.first_name" placeholder="Nombre(s)" required class="input-field" />
          <input type="text" v-model="formEmp.last_name" placeholder="Apellidos" required class="input-field" />
          <input type="text" v-model="formEmp.phone" placeholder="Teléfono" class="input-field" />
          <input type="text" v-model="formEmp.job_title" placeholder="Puesto (Job Title)" class="input-field" />
          <button type="submit" class="btn btn-save">Crear Empleado</button>
        </form>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr><th>ID</th><th>User ID</th><th>Nombre Completo</th><th>Teléfono</th><th>Puesto</th></tr>
          </thead>
          <tbody>
            <tr v-for="emp in employees" :key="emp.id">
              <td>{{ emp.id }}</td>
              <td>{{ emp.user_id }}</td>
              <td><strong>{{ emp.first_name }} {{ emp.last_name }}</strong></td>
              <td>{{ emp.phone || 'N/D' }}</td>
              <td>{{ emp.job_title || 'N/D' }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const seccionActual = ref('roles')
const roles = ref([])
const users = ref([])
const employees = ref([])

const error = ref(null)
const success = ref(null)

const formRol = ref({ name: '', description: '' })
const formUser = ref({ username: '', email: '', password_hash: '', role_id: '' })
const formEmp = ref({ user_id: '', first_name: '', last_name: '', phone: '', job_title: '' })

const API_ROLES = 'http://localhost:3000/api/v2/roles'
const API_USERS = 'http://localhost:3000/api/v2/users'
const API_EMPLOYEES = 'http://localhost:3000/api/v2/employees'

const cargarDatos = async () => {
  error.value = null
  
  try {
    const resRoles = await axios.get(API_ROLES)
    roles.value = Array.isArray(resRoles.data) ? resRoles.data : (resRoles.data.data || [])
  } catch (err) {
    console.log('Error cargando roles:', err.message)
  }

  try {
    const resUsers = await axios.get(API_USERS)
    users.value = Array.isArray(resUsers.data) ? resUsers.data : (resUsers.data.data || resUsers.data.usuarios || [])
  } catch (err) {
    console.log('Error cargando usuarios:', err.message)
  }

  try {
    const resEmp = await axios.get(API_EMPLOYEES)
    employees.value = Array.isArray(resEmp.data) ? resEmp.data : (resEmp.data.data || resEmp.data.employees || [])
  } catch (err) {
    console.log('Error cargando empleados:', err.message)
  }
}

const guardarRol = async () => {
  try {
    await axios.post(API_ROLES, formRol.value)
    success.value = 'Rol creado exitosamente.'
    error.value = null
    formRol.value = { name: '', description: '' }
    cargarDatos()
  } catch (err) {
    error.value = 'Error al crear rol: ' + (err.response?.data?.message || err.message)
  }
}

const guardarUsuario = async () => {
  try {
    await axios.post(API_USERS, formUser.value)
    success.value = 'Usuario creado exitosamente.'
    error.value = null
    formUser.value = { username: '', email: '', password_hash: '', role_id: '' }
    cargarDatos()
  } catch (err) {
    error.value = 'Error al crear usuario: ' + (err.response?.data?.message || err.response?.data?.error || err.message)
  }
}

const guardarEmpleado = async () => {
  try {
    await axios.post(API_EMPLOYEES, formEmp.value)
    success.value = 'Empleado creado exitosamente.'
    error.value = null
    formEmp.value = { user_id: '', first_name: '', last_name: '', phone: '', job_title: '' }
    cargarDatos()
  } catch (err) {
    error.value = 'Error al crear empleado: ' + (err.response?.data?.error || err.response?.data?.message || err.message)
  }
}

onMounted(() => {
  cargarDatos()
})
</script>