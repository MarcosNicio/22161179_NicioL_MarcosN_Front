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
          <input type="text" v-model="formRol.name" placeholder="Nombre (ej. admin, cashier)" required class="input-field" />
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
            <tr v-for="rol in roles" :key="rol.id || rol.ID">
              <td>{{ rol.id ?? rol.ID ?? 'N/D' }}</td>
              <td><strong>{{ rol.name ?? rol.nombre ?? rol.NAME ?? 'Sin nombre' }}</strong></td>
              <td>{{ rol.description ?? rol.descripcion ?? rol.DESCRIPTION ?? 'Sin descripción' }}</td>
            </tr>
            <tr v-if="roles.length === 0">
              <td colspan="3" style="text-align: center; color: #9ca3af;">No hay roles registrados.</td>
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
          <input type="text" v-model="formUser.username" placeholder="Username" required class="input-field" />
          <input type="email" v-model="formUser.email" placeholder="Correo electrónico" required class="input-field" />
          <input type="password" v-model="formUser.password" placeholder="Contraseña" required class="input-field" />
          <input type="number" v-model="formUser.role_id" placeholder="ID del Rol (ej. 1, 2, 3)" required class="input-field" />
          <button type="submit" class="btn btn-save">Crear Usuario</button>
        </form>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr><th>ID</th><th>Username / Nombre</th><th>Email</th><th>Role ID</th></tr>
          </thead>
          <tbody>
            <tr v-for="user in users" :key="user.id || user.ID">
              <td>{{ user.id ?? user.ID ?? 'N/D' }}</td>
              <td><strong>{{ user.username ?? user.nombre ?? user.NAME ?? 'N/D' }}</strong></td>
              <td>{{ user.email ?? user.correo ?? 'N/D' }}</td>
              <td>{{ user.role_id ?? user.ROLE_ID ?? '1' }}</td>
            </tr>
            <tr v-if="users.length === 0">
              <td colspan="4" style="text-align: center; color: #9ca3af;">No hay usuarios registrados.</td>
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
          <input type="number" v-model="formEmp.user_id" placeholder="ID de Usuario asociado" required class="input-field" />
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
            <tr><th>ID</th><th>Nombre Completo</th><th>Teléfono</th><th>Puesto</th></tr>
          </thead>
          <tbody>
            <tr v-for="emp in employees" :key="emp.id || emp.ID">
              <td>{{ emp.id ?? emp.ID ?? 'N/D' }}</td>
              <td><strong>{{ emp.first_name ?? emp.nombre ?? 'N/D' }} {{ emp.last_name ?? '' }}</strong></td>
              <td>{{ emp.phone ?? emp.telefono ?? 'N/D' }}</td>
              <td>{{ emp.job_title ?? emp.puesto ?? 'N/D' }}</td>
            </tr>
            <tr v-if="employees.length === 0">
              <td colspan="4" style="text-align: center; color: #9ca3af;">No hay empleados registrados.</td>
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
const formUser = ref({ username: '', email: '', password: '', role_id: '' })
const formEmp = ref({ user_id: '', first_name: '', last_name: '', phone: '', job_title: '' })

const API_ROLES = 'http://localhost:3000/api/v2/roles'
const API_USERS = 'http://localhost:3000/api/v2/users'
const API_EMPLOYEES = 'http://localhost:3000/api/v2/employees'

const cargarDatos = async () => {
  try {
    const [resRoles, resUsers, resEmp] = await Promise.allSettled([
      axios.get(API_ROLES),
      axios.get(API_USERS),
      axios.get(API_EMPLOYEES)
    ])

    if (resRoles.status === 'fulfilled') {
      roles.value = Array.isArray(resRoles.value.data) ? resRoles.value.data : (resRoles.value.data.data || [])
    }
    if (resUsers.status === 'fulfilled') {
      users.value = Array.isArray(resUsers.value.data) ? resUsers.value.data : (resUsers.value.data.data || resUsers.value.data.usuarios || [])
    }
    if (resEmp.status === 'fulfilled') {
      employees.value = Array.isArray(resEmp.value.data) ? resEmp.value.data : (resEmp.value.data.data || resEmp.value.data.employees || [])
    }

    error.value = null
  } catch (err) {
    error.value = 'Error al sincronizar datos.'
  }
}

const guardarRol = async () => {
  try {
    await axios.post(API_ROLES, formRol.value)
    success.value = 'Rol creado exitosamente.'
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
    formUser.value = { username: '', email: '', password: '', role_id: '' }
    cargarDatos()
  } catch (err) {
    error.value = 'Error al crear usuario: ' + (err.response?.data?.message || err.message)
  }
}

const guardarEmpleado = async () => {
  try {
    await axios.post(API_EMPLOYEES, formEmp.value)
    success.value = 'Empleado creado exitosamente.'
    formEmp.value = { user_id: '', first_name: '', last_name: '', phone: '', job_title: '' }
    cargarDatos()
  } catch (err) {
    error.value = 'Error al crear empleado: ' + (err.response?.data?.message || err.message)
  }
}

onMounted(() => {
  cargarDatos()
})
</script>