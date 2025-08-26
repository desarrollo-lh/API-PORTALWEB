# API Web Base - Sistema de Gestión de Usuarios y Sucursales

API REST desarrollada en Flask para la gestión de usuarios, autenticación y sucursales. Sistema adaptado para necesidades específicas de gestión empresarial.

## 🚀 Características

- **API REST** con Flask y JWT
- **Autenticación segura** con tokens JWT
- **Gestión de usuarios** con roles y perfiles
- **Gestión de sucursales** con permisos por usuario
- **Soporte CORS** configurado
- **Estructura modular** con blueprints
- **Base de datos MySQL** con conexión optimizada

## 📋 Requisitos Previos

- Python 3.8+
- MySQL 8.0+
- Git

## 🛠️ Instalación

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/mbravot/API_WEB_BASE.git
   cd API_WEB_BASE
   ```

2. **Crear entorno virtual:**
   ```bash
   python -m venv venv
   ```

3. **Activar el entorno virtual:**
   - **Windows:**
     ```bash
     .\venv\Scripts\Activate.ps1
     ```
   - **Linux/Mac:**
     ```bash
     source venv/bin/activate
     ```

4. **Instalar dependencias:**
   ```bash
   pip install -r requirements.txt
   ```

## ⚙️ Configuración

### 1. Variables de Entorno

Crear un archivo `.env` en la raíz del proyecto:

```env
# Configuración de la aplicación
FLASK_ENV=development
SECRET_KEY=tu-clave-secreta-super-segura
JWT_SECRET_KEY=tu-jwt-secret-key

# Configuración de la base de datos
DB_HOST=localhost
DB_USER=tu_usuario
DB_PASSWORD=tu_password
DB_NAME=tu_base_de_datos
DATABASE_URL=mysql://usuario:password@localhost/nombre_db

# Configuración del servidor
PORT=5000
DEBUG=True
```

### 2. Base de Datos

Asegúrate de que las siguientes tablas existan en tu base de datos MySQL:

```sql
-- Tabla de usuarios (estructura actualizada)
CREATE TABLE `general_dim_usuario` (
  `id` varchar(45) NOT NULL,
  `id_sucursalactiva` int NOT NULL,
  `usuario` varchar(45) NOT NULL,
  `nombre` varchar(45) NOT NULL,
  `apellido_paterno` varchar(45) NOT NULL,
  `apellido_materno` varchar(45) DEFAULT NULL,
  `clave` varchar(255) NOT NULL,
  `fecha_creacion` date NOT NULL,
  `id_estado` int NOT NULL DEFAULT '1',
  `correo` varchar(100) NOT NULL,
  `id_rol` int NOT NULL DEFAULT '3',
  `id_perfil` int NOT NULL DEFAULT '1',
  PRIMARY KEY (`id`),
  UNIQUE KEY `usuario_UNIQUE` (`usuario`)
);

-- Tabla de sucursales
CREATE TABLE `general_dim_sucursal` (
  `id` int NOT NULL AUTO_INCREMENT,
  `nombre` varchar(100) NOT NULL,
  `ubicacion` varchar(100) DEFAULT NULL,
  `id_sucursaltipo` int DEFAULT '1',
  PRIMARY KEY (`id`)
);

-- Tabla pivot para relación usuario-sucursal
CREATE TABLE `usuario_pivot_sucursal_usuario` (
  `id_sucursal` int NOT NULL,
  `id_usuario` varchar(45) NOT NULL,
  PRIMARY KEY (`id_sucursal`, `id_usuario`)
);
```

## 🚀 Ejecución

```bash
python app.py
```

La API estará disponible en `http://localhost:5000`

## 📚 Endpoints de la API

### 🔐 Autenticación

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `POST` | `/api/auth/login` | Iniciar sesión |
| `POST` | `/api/auth/refresh` | Refrescar token |
| `POST` | `/api/auth/cambiar-clave` | Cambiar contraseña |
| `POST` | `/api/auth/cambiar-sucursal` | Cambiar sucursal activa |
| `GET` | `/api/auth/me` | Obtener información del usuario |
| `PUT` | `/api/auth/me` | Actualizar información del usuario |

### 🏢 Sucursales

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/api/sucursales/` | Obtener sucursales del usuario |
| `GET` | `/api/opciones/sucursales` | Obtener sucursales del usuario |

### 👥 Usuarios (Solo Administradores)

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/api/usuarios/sucursales` | Obtener todas las sucursales |
| `GET` | `/api/usuarios/<id>/sucursales-permitidas` | Obtener sucursales de un usuario |
| `POST` | `/api/usuarios/<id>/sucursales-permitidas` | Asignar sucursales a un usuario |
| `DELETE` | `/api/usuarios/<id>/sucursales-permitidas` | Eliminar sucursales de un usuario |

### 🔧 Utilidades

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/api/test-db` | Probar conexión a base de datos |
| `GET` | `/api/config` | Ver configuración del sistema |

## 📝 Ejemplos de Uso

### Login de Usuario

```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "usuario": "usuario123",
    "clave": "password123"
  }'
```

**Respuesta:**
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "usuario": "usuario123",
  "nombre": "Juan",
  "id_sucursal": 1,
  "sucursal_nombre": "Sucursal Centro",
  "sucursal_comuna": "Santiago Centro",
  "id_rol": 3,
  "id_perfil": 1
}
```

### Obtener Sucursales del Usuario

```bash
curl -X GET http://localhost:5000/api/sucursales/ \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Cambiar Sucursal Activa

```bash
curl -X POST http://localhost:5000/api/auth/cambiar-sucursal \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "id_sucursal": 2
  }'
```

## 🏗️ Estructura del Proyecto

```
API_WEB_BASE/
├── app.py                 # Aplicación principal Flask
├── config.py              # Configuración del sistema
├── requirements.txt       # Dependencias Python
├── README.md             # Documentación
├── .env                  # Variables de entorno (crear)
├── venv/                 # Entorno virtual
├── blueprints/           # Módulos de la API
│   ├── __init__.py
│   ├── auth.py          # Autenticación y usuarios
│   ├── opciones.py      # Opciones y sucursales
│   └── usuarios.py      # Gestión de usuarios (admin)
└── utils/               # Utilidades
    ├── __init__.py
    ├── db.py            # Conexión a base de datos
    └── validar_rut.py   # Validación de RUT
```

## 🔐 Autenticación

La API utiliza **JWT (JSON Web Tokens)** para la autenticación:

1. **Login:** El usuario obtiene un token de acceso
2. **Requests:** Incluir el token en el header `Authorization: Bearer <token>`
3. **Refresh:** Renovar el token antes de que expire

### Headers Requeridos

```http
Authorization: Bearer <tu_token_jwt>
Content-Type: application/json
```

## 🛡️ Seguridad

- **JWT Tokens** para autenticación
- **Bcrypt** para hash de contraseñas
- **CORS** configurado para desarrollo
- **Validación de permisos** por rol y perfil
- **Verificación de acceso** a sucursales

## 🚀 Despliegue

### Desarrollo Local
```bash
python app.py
```

### Producción
```bash
# Configurar variables de entorno de producción
export FLASK_ENV=production
export DEBUG=False

# Ejecutar con gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

## 📊 Monitoreo

- **Health Check:** `/api/test-db`
- **Configuración:** `/api/config`
- **Logs:** Configurados en `app.py`

## 🤝 Contribución

1. Fork el proyecto
2. Crear una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abrir un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo `LICENSE` para más detalles.

## 📞 Soporte

Para soporte técnico o preguntas sobre la API, contactar al equipo de desarrollo.

---

**Desarrollado con ❤️ por el equipo de desarrollo** # API-PORTALWEB
