# Panel Frontend - Sistema de Gestion (Roles, Usuarios y Empleados)

Este repositorio contiene la interfaz de usuario desarrollada con Vue 3 y Vite, conectada a una API RESTful con backend en Node.js/Express y base de datos relacional en PostgreSQL.

---

## Tecnologias Utilizadas

* Vue 3 (Composition API con script setup)
* Vite (Empaquetador y servidor de desarrollo rapido)
* Axios (Cliente HTTP para el consumo de la API REST)
* CSS3 (Diseno responsivo y personalizado)

---

## Requisitos Previos

Asegurate de tener instalado en tu equipo:
* Node.js (Version 18 o superior recomendada)
* Git

---

## Instalacion y Configuracion del Proyecto

1. Clona este repositorio:
   ```bash
   git clone https://github.com/MarcosNicio/22161179_NicioL_MarcosN_Front.git
   ```

2. Entra al directorio del proyecto:
   ```bash
   cd 22161179_NicioL_MarcosN_Front
   ```

3. Instala las dependencias:
   ```bash
   npm install
   ```

---

## Ejecucion del Servidor de Desarrollo

Para iniciar la aplicacion localmente en modo de desarrollo, ejecuta:

```bash
npm run dev
```

La aplicacion estara disponible por defecto en el navegador en la siguiente direccion:
* http://localhost:5173

---

## Funcionalidades del Sistema

* Gestion de Roles: Visualizacion de roles disponibles y formulario para la creacion de nuevos registros.
* Gestion de Usuarios: Listado de usuarios del sistema y formulario de registro vinculado a roles mediante llaves foraneas.
* Gestion de Empleados: Registro y administracion de datos de empleados vinculados a usuarios existentes del sistema mediante selectores dinamicos.
* Manejo de Errores: Captura y visualizacion de respuestas del servidor ante restricciones de bases de datos o fallos de conexion.
