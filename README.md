# Sistema de Registro de Personas (Google Sheets API v4)

Este proyecto es una aplicación web segura para la gestión y registro de personas, utilizando una hoja de cálculo de **Google Sheets** como base de datos relacional a través de la API oficial de Google Workspace. 

El sistema cuenta con un flujo de trabajo estructurado y separación de funciones mediante tres roles de usuario (Administrador, Registrador de Datos y Supervisor).

## 架构 & Características
- **Base de Datos No-Convencional:** Persistencia completa en Google Sheets sin necesidad de configurar servidores de bases de datos tradicionales (SQL/NoSQL).
- **Seguridad RBAC:** Control de acceso estricto por roles en el Backend y Frontend.
- **Flujo de Validación:** Los registros nacen en estado *Pendiente*, son evaluados por un *Supervisor* para ser *Aprobados* o *Rechazados*.
- **Autenticación:** Inicio de sesión seguro con almacenamiento de sesiones/tokens de corta duración.

---

## 📊 Estructura de la Base de Datos (Google Sheets)

Para que la aplicación funcione, tu archivo de Google Sheets debe contener exactamente las siguientes dos pestañas con sus respectivos encabezados en la primera fila:

### 1. Hoja: `Usuarios`
Maneja las credenciales y los niveles de acceso al sistema.

| Columna | Tipo | Descripción |
| :--- | :--- | :--- |
| `ID_Usuario` | Texto / UUID | Identificador único del usuario del sistema. |
| `Nombre` | Texto | Nombre completo del operador. |
| `Correo_Electronico` | Texto | Correo usado para el login (Llave única). |
| `Contrasena_Hash` | Texto | Hash seguro de la contraseña. |
| `Rol` | Enumerado | Valores estrictos: `Administrador`, `Registrador de datos`, `Supervisor`. |
| `Estado` | Enumerado | `Activo` o `Inactivo`. |

### 2. Hoja: `Registro_Personas`
Almacena los datos de los ciudadanos o personas registradas en el programa.

| Columna | Tipo | Descripción |
| :--- | :--- | :--- |
| `ID_Registro` | Texto / UUID | Identificador único del registro de la persona. |
| `Nombres` | Texto | Nombres de la persona registrada. |
| `Apellidos` | Texto | Apellidos de la persona registrada. |
| `Fecha_Nacimiento` | Fecha (YYYY-MM-DD) | Fecha de nacimiento. |
| `Tipo_Documento` | Enumerado | `TI` (Tarjeta de Identidad) o `CC` (Cédula de Ciudadanía). |
| `Numero_Documento` | Texto / Numérico | Número de identificación único. |
| `Telefono` | Texto | Número de contacto. |
| `Correo` | Texto | Correo electrónico de la persona registrada. |
| `Estado_Registro` | Enumerado | `Pendiente`, `Aprobado` o `Rechazado`. |
| `Creado_Por` | Texto | `ID_Usuario` del Registrador de datos que ingresó el registro. |
| `Validado_Por` | Texto | `ID_Usuario` del Supervisor que aprobó/rechazó el registro. |
| `Fecha_Creacion` | Timestamp | Fecha y hora exacta de inserción. |
| `Observaciones` | Texto | Detalles o motivos del cambio de estado (crítico si es Rechazado). |

---

## 🛠️ Requisitos Previos y Configuración de Google Cloud

1. **Crear el Proyecto en Google Cloud:**
   - Ve a [Google Cloud Console](https://console.cloud.google.com/).
   - Crea un nuevo proyecto llamado `Registro-Personas-Sheets`.

2. **Habilitar la API:**
   - En el buscador de la consola, escribe **Google Sheets API** y haz clic en **Habilitar**.

3. **Crear Cuenta de Servicio (Service Account):**
   - Ve a **API y servicios** > **Credenciales**.
   - Haz clic en **Crear credenciales** y selecciona **Cuenta de servicio**.
   - Asígnale un nombre (ej. `sheets-operator`) y finaliza el asistente.
   - Entra a la cuenta de servicio creada, ve a la pestaña **Claves**, haz clic en **Agregar clave** > **Crear clave nueva** en formato **JSON**.
   - Descarga el archivo JSON. Contiene la llave privada que tu backend usará para autenticarse.

4. **Compartir la Hoja de Google Sheets:**
   - Abre tu archivo JSON descargado y copia el valor del campo `"client_email"` (ej. `sheets-operator@tu-proyecto.iam.gserviceaccount.com`).
   - Abre tu documento de Google Sheets en el navegador.
   - Haz clic en el botón **Compartir** (esquina superior derecha).
   - Pega el correo de la cuenta de servicio y asígnale el rol de **Editor**. ¡Este paso es indispensable para que la app pueda escribir datos!
   - Copia el **ID de la hoja de cálculo** de la URL de tu navegador: `https://docs.google.com/spreadsheets/d/[ESTE_ES_EL_ID]/edit`.

---

## 🚀 Instalación y Despliegue Local

### 1. Clonar el repositorio e instalar dependencias
```bash
git clone https://github.com/tu-usuario/registro-sheets-app.git
cd registro-sheets-app
npm install  # Si usas Node.js
# o pip install -r requirements.txt si usas Python
