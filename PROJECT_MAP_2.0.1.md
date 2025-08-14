# 🗺️ ZatoBox v2.0 - Mapeo Completo del Proyecto

## 📋 Índice del Mapeo

- [🏗️ Arquitectura General](#️-arquitectura-general)
- [🔌 Endpoints y APIs](#-endpoints-y-apis)
- [🔄 Flujo de Datos](#-flujo-de-datos)
- [📁 Estructura de Archivos](#-estructura-de-archivos)
- [🔗 Conexiones y Dependencias](#-conexiones-y-dependencias)
- [🚀 Flujos de Usuario](#-flujos-de-usuario)
- [⚙️ Configuración y Variables](#️-configuración-y-variables)
- [🧪 Testing y Validación](#-testing-y-validación)

---

## 🏗️ Arquitectura General

### **Diagrama de Arquitectura**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              ZatoBox v2.0                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────────┐ │
│  │   Frontend      │    │    Backend      │    │         OCR             │ │
│  │   React + TS    │◄──►│   FastAPI       │◄──►│   Flask + AI            │ │
│  │   (Puerto 5173) │    │   (Puerto 4444) │    │   (Puerto 8001)        │ │
│  └─────────────────┘    └─────────────────┘    └─────────────────────────┘ │
│           │                       │                       │                 │
│           │                       │                       │                 │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────────┐ │
│  │   Admin         │    │   Base de       │    │   Archivos              │ │
│  │   Dashboard     │    │   Datos         │    │   Subidos               │ │
│  │   Streamlit     │    │   MySQL/PostgreSQL│  │   (uploads/)            │ │
│  │   (Puerto 8501) │    │                 │    │                         │ │
│  └─────────────────┘    └─────────────────┘    └─────────────────────────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### **Componentes Principales**

| Componente | Tecnología | Puerto | Descripción |
|------------|------------|---------|-------------|
| **Frontend** | React 18.3 + TypeScript + Tailwind CSS | 5173 | Interfaz de usuario principal |
| **Backend API** | FastAPI + Python | 4444 | API REST para operaciones CRUD |
| **OCR Service** | Flask + Python + Tesseract | 8001 | Procesamiento de documentos |
| **Admin Dashboard** | Streamlit + Python | 8501 | Panel administrativo |
| **Base de Datos** | MySQL/PostgreSQL | 3306/5432 | Almacenamiento persistente |

---

## 🔌 Endpoints y APIs

### **1. Backend API (FastAPI) - Puerto 4444**

#### **🔐 Autenticación (`/api/auth`)**
```typescript
// Base URL: http://localhost:4444/api/auth

POST   /login                    // Iniciar sesión
POST   /register                 // Registrar usuario
POST   /logout                   // Cerrar sesión
GET    /me                       // Obtener usuario actual
GET    /users                    // Listar usuarios (admin)
GET    /profile/{user_id}        // Obtener perfil
PUT    /profile/{user_id}        // Actualizar perfil
```

#### **📦 Productos (`/api/products`)**
```typescript
// Base URL: http://localhost:4444/api/products

POST   /                         // Crear producto
GET    /{product_id}            // Obtener producto
PUT    /{product_id}            // Actualizar producto
DELETE /{product_id}            // Eliminar producto
```

#### **📊 Inventario (`/api/inventory`)**
```typescript
// Base URL: http://localhost:4444/api/inventory

GET    /                         // Obtener inventario completo
PUT    /{product_id}            // Actualizar stock
```

#### **💰 Ventas (`/api/sales`)**
```typescript
// Base URL: http://localhost:4444/api/sales

GET    /                         // Listar ventas
GET    /{sale_id}               // Obtener venta
POST   /                         // Crear venta
PUT    /{sale_id}               // Actualizar venta
DELETE /{sale_id}               // Eliminar venta
GET    /stats                    // Estadísticas de ventas
```

### **2. OCR Service (Flask) - Puerto 8001**

#### **🔍 Procesamiento de Documentos**
```typescript
// Base URL: http://127.0.0.1:8001/api/v1

GET    /health                   // Verificación de estado
POST   /invoice/process          // Procesar factura/documento
GET    /invoice/debug            // Información de debug
GET    /invoice/supported-formats // Formatos soportados
```

### **3. Frontend API Client**

#### **🔐 Servicios de Autenticación**
```typescript
// authAPI
authAPI.login(credentials)       // Iniciar sesión
authAPI.register(userData)       // Registrar usuario
authAPI.logout()                 // Cerrar sesión
authAPI.getCurrentUser()         // Obtener usuario actual
```

#### **📦 Servicios de Productos**
```typescript
// productsAPI
productsAPI.getAll(params)       // Obtener todos los productos
productsAPI.getById(id)          // Obtener producto por ID
productsAPI.create(productData)  // Crear producto
productsAPI.uploadImages(id, formData) // Subir imágenes
```

#### **🔍 Servicios OCR**
```typescript
// ocrAPI
ocrAPI.processDocument(file, options)    // Procesar documento
ocrAPI.getDebugInfo()                    // Obtener info de debug
ocrAPI.getSupportedFormats()             // Formatos soportados
```

---

## 🔄 Flujo de Datos

### **1. Flujo de Autenticación**

```mermaid
sequenceDiagram
    participant U as Usuario
    participant F as Frontend
    participant B as Backend
    participant DB as Base de Datos

    U->>F: Ingresa credenciales
    F->>B: POST /api/auth/login
    B->>DB: Verificar usuario
    DB->>B: Datos del usuario
    B->>B: Generar JWT
    B->>F: Token + datos usuario
    F->>F: Almacenar token en localStorage
    F->>U: Redirigir al dashboard
```

### **2. Flujo de Creación de Producto**

```mermaid
sequenceDiagram
    participant U as Usuario
    participant F as Frontend
    participant B as Backend
    participant DB as Base de Datos

    U->>F: Completa formulario de producto
    F->>F: Validar datos
    F->>B: POST /api/products (FormData)
    B->>B: Validar JWT
    B->>B: Procesar imágenes
    B->>DB: INSERT INTO products
    DB->>B: ID del producto creado
    B->>F: Confirmación + datos del producto
    F->>U: Mostrar mensaje de éxito
```

### **3. Flujo de Procesamiento OCR**

```mermaid
sequenceDiagram
    participant U as Usuario
    participant F as Frontend
    participant O as OCR Service
    participant B as Backend

    U->>F: Sube documento/factura
    F->>F: Preparar FormData
    F->>O: POST /api/v1/invoice/process
    O->>O: Preprocesar imagen
    O->>O: Ejecutar Tesseract OCR
    O->>O: Extraer metadatos
    O->>O: Extraer items de línea
    O->>F: Resultado estructurado
    F->>F: Mostrar datos extraídos
    F->>U: Permitir edición/confirmación
```

---

## 📁 Estructura de Archivos

### **Frontend (`/frontend`)**
```
frontend/
├── src/
│   ├── components/              # Componentes de UI
│   │   ├── HomePage.tsx         # Página principal
│   │   ├── InventoryPage.tsx    # Gestión de inventario
│   │   ├── LoginPage.tsx        # Autenticación
│   │   ├── ProductCard.tsx      # Tarjeta de producto
│   │   ├── SideMenu.tsx         # Menú lateral
│   │   ├── NewProductPage.tsx   # Crear producto
│   │   ├── EditProductPage.tsx  # Editar producto
│   │   ├── OCRResultPage.tsx    # Resultados OCR
│   │   ├── SmartInventoryPage.tsx # Inventario inteligente
│   │   ├── ProfilePage.tsx      # Perfil de usuario
│   │   ├── SettingsPage.tsx     # Configuraciones
│   │   ├── PluginStorePage.tsx  # Tienda de plugins
│   │   ├── PaymentScreen.tsx    # Pantalla de pago
│   │   ├── PaymentSuccessScreen.tsx # Confirmación de pago
│   │   ├── SalesDrawer.tsx      # Cajón de ventas
│   │   └── ProtectedRoute.tsx   # Ruta protegida
│   ├── contexts/                # Contextos de estado
│   │   ├── AuthContext.tsx      # Contexto de autenticación
│   │   └── PluginContext.tsx    # Contexto de plugins
│   ├── services/                # Servicios de API
│   │   └── api.ts              # Cliente HTTP centralizado
│   ├── config/                  # Configuración
│   │   └── api.ts              # URLs y configuración de API
│   ├── App.tsx                  # Componente principal
│   ├── main.tsx                 # Punto de entrada
│   └── index.css                # Estilos globales
├── package.json                 # Dependencias y scripts
├── vite.config.ts               # Configuración de Vite
├── tailwind.config.js           # Configuración de Tailwind
└── tsconfig.json                # Configuración de TypeScript
```

### **Backend (`/backend/zato-csm-backend`)**
```
backend/zato-csm-backend/
├── models/                      # Modelos de datos
│   ├── user.py                 # Modelo de usuario
│   ├── product.py              # Modelo de producto
│   └── sales.py                # Modelo de ventas
├── routes/                      # Endpoints de API
│   ├── auth.py                 # Rutas de autenticación
│   ├── products.py             # Rutas de productos
│   ├── inventory.py            # Rutas de inventario
│   └── sales.py                # Rutas de ventas
├── services/                    # Lógica de negocio
│   ├── auth_service.py         # Servicio de autenticación
│   ├── product_service.py      # Servicio de productos
│   ├── inventory_service.py    # Servicio de inventario
│   └── sales_service.py        # Servicio de ventas
├── repositories/                # Acceso a datos
│   ├── base_repository.py      # Repositorio base
│   ├── user_repositories.py    # Repositorio de usuarios
│   ├── product_repositories.py # Repositorio de productos
│   └── sales_repositories.py   # Repositorio de ventas
├── config/                      # Configuración
│   ├── database.py             # Configuración de BD
│   └── settings.py             # Configuraciones generales
├── utils/                       # Utilidades
│   ├── dependencies.py         # Dependencias de FastAPI
│   └── timezone_utils.py       # Utilidades de zona horaria
├── main.py                     # Aplicación principal FastAPI
├── run.py                      # Script de ejecución
└── requeriments.txt            # Dependencias de Python
```

### **OCR Service (`/OCR`)**
```
OCR/
├── app-light-fixed.py           # Aplicación principal OCR
├── app-light-fixed-clean.py     # Versión optimizada y limpia
├── hybrid_intelligent_system.py # Sistema híbrido de IA
├── robust_multi_engine_ocr.py   # OCR multi-motor robusto
├── backend.py                   # Backend del servicio OCR
├── requirements-light.txt        # Dependencias optimizadas
├── uploads/                     # Archivos subidos
│   ├── products/                # Imágenes de productos
│   └── ocr/                     # Documentos para procesamiento OCR
└── outputs/                     # Resultados del procesamiento
```

### **Admin Dashboard (`/backend/admin-dashboard`)**
```
backend/admin-dashboard/
├── main.py                      # Aplicación principal Streamlit
├── config.py                    # Configuración del dashboard
├── auth/                        # Autenticación
│   ├── login.py                # Interfaz de login
│   └── session.py              # Gestión de sesión
├── components/                  # Componentes base
│   ├── layout.py               # Layout principal
│   ├── base_pages.py           # Páginas base
│   ├── forms.py                # Formularios
│   ├── language_detector.py    # Detección de idioma
│   └── translations.py         # Sistema de traducción
├── page_modules/                # Módulos de páginas
│   ├── dashboard.py            # Dashboard principal
│   ├── users.py                # Gestión de usuarios
│   ├── products.py             # Gestión de productos
│   ├── reports.py              # Reportes y análisis
│   └── settings.py             # Configuraciones
├── services/                    # Servicios
│   └── api_client.py           # Cliente de API
└── README.md                    # Documentación del dashboard
```

---

## 🔗 Conexiones y Dependencias

### **1. Dependencias del Frontend**
```json
{
  "react": "^18.3.1",
  "react-dom": "^18.3.1",
  "react-router-dom": "^6.30.1",
  "lucide-react": "^0.344.0",
  "tailwindcss": "^3.4.17",
  "typescript": "^5.6.3",
  "vite": "^5.4.8"
}
```

### **2. Dependencias del Backend**
```txt
fastapi==0.116.1
uvicorn==0.35.0
PyMySQL==1.1.1
psycopg2-binary==2.9.10
PyJWT==2.10.1
pydantic==2.11.7
python-multipart==0.0.20
```

### **3. Dependencias del OCR**
```txt
Flask==2.3.3
Flask-CORS==4.0.0
pytesseract==0.3.10
opencv-python-headless==4.11.0.86
Pillow>=11.3.0
numpy>=2.3.2
pdf2image==1.16.3
```

### **4. Conexiones de Base de Datos**

#### **MySQL**
```python
DATABASE_URL = "mysql://usuario:password@localhost/zatobox"
```

#### **PostgreSQL**
```python
DATABASE_URL = "postgresql://usuario:password@localhost/zatobox"
```

### **5. Variables de Entorno**
```env
# Backend
DATABASE_URL=mysql://usuario:password@localhost/zatobox
JWT_SECRET_KEY=tu_clave_secreta_aqui
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# OCR
TESSERACT_PATH=C:\Program Files\Tesseract-OCR\tesseract.exe
UPLOAD_FOLDER=./uploads
OUTPUT_FOLDER=./outputs

# Frontend
VITE_API_URL=http://localhost:4444/api
VITE_OCR_API_URL=http://127.0.0.1:8001/api/v1
```

---

## 🚀 Flujos de Usuario

### **1. Flujo de Registro e Inicio de Sesión**
```
1. Usuario accede a /login
2. Ingresa credenciales
3. Frontend envía POST a /api/auth/login
4. Backend valida credenciales
5. Se genera JWT y se almacena en localStorage
6. Usuario es redirigido al dashboard
```

### **2. Flujo de Gestión de Productos**
```
1. Usuario navega a /inventory
2. Puede ver lista de productos existentes
3. Clic en "Nuevo Producto" → /new-product
4. Completa formulario y sube imágenes
5. Frontend envía POST a /api/products
6. Backend procesa y almacena en BD
7. Usuario es redirigido al inventario
```

### **3. Flujo de Procesamiento OCR**
```
1. Usuario navega a /smart-inventory
2. Sube factura/documento
3. Frontend envía archivo a OCR Service
4. OCR procesa imagen con Tesseract
5. Se extraen metadatos e items
6. Resultado se muestra en /ocr-result
7. Usuario puede editar y confirmar
8. Datos se envían al backend para almacenamiento
```

### **4. Flujo de Administración**
```
1. Admin accede a /admin-dashboard
2. Se autentica con credenciales de admin
3. Puede gestionar usuarios, productos, ventas
4. Accede a reportes y estadísticas
5. Configura parámetros del sistema
```

---

## ⚙️ Configuración y Variables

### **1. Configuración del Frontend**
```typescript
// config/api.ts
export const API_CONFIG = {
  BASE_URL: 'http://localhost:4444/api',
  OCR_BASE_URL: 'http://127.0.0.1:8001/api/v1',
  TIMEOUT: 60000,
  HEADERS: {
    'Content-Type': 'application/json',
  },
};
```

### **2. Configuración del Backend**
```python
# config/settings.py
DATABASE_URL = os.getenv("DATABASE_URL")
JWT_SECRET_KEY = os.getenv("JWT_SECRET_KEY")
JWT_ALGORITHM = os.getenv("JWT_ALGORITHM", "HS256")
ACCESS_TOKEN_EXPIRE_MINUTES = int(os.getenv("ACCESS_TOKEN_EXPIRE_MINUTES", "30"))
```

### **3. Configuración del OCR**
```python
# Configuración de Tesseract
TESSERACT_PATH = "C:\\Program Files\\Tesseract-OCR\\tesseract.exe"
UPLOAD_FOLDER = "./uploads"
OUTPUT_FOLDER = "./outputs"
```

### **4. Puertos y URLs de Acceso**
| Servicio | Puerto | URL | Descripción |
|----------|--------|-----|-------------|
| Frontend | 5173 | http://localhost:5173 | Interfaz principal |
| Backend API | 4444 | http://localhost:4444 | API REST |
| OCR Service | 8001 | http://127.0.0.1:8001 | Servicio OCR |
| Admin Dashboard | 8501 | http://localhost:8501 | Panel administrativo |
| API Docs | 4444 | http://localhost:4444/docs | Documentación Swagger |

---

## 🧪 Testing y Validación

### **1. Frontend Testing**
```bash
# Ejecutar tests
npm run test

# Tests con UI
npm run test:ui

# Cobertura de tests
npm run test:coverage

# Linting
npm run lint
npm run lint:fix
```

### **2. Backend Testing**
```bash
# Instalar pytest
pip install pytest

# Ejecutar tests
pytest

# Tests con cobertura
pytest --cov=app tests/
```

### **3. Validación de APIs**
```bash
# Verificar estado del backend
curl http://localhost:4444/

# Verificar estado del OCR
curl http://127.0.0.1:8001/health

# Verificar documentación de API
curl http://localhost:4444/docs
```

---

## 🔧 Scripts de Automatización

### **1. Instalación Automática**
```powershell
# Script principal de instalación
.\install-zatobox.ps1

# Funciones principales:
# - Verificar requisitos del sistema
# - Instalar Python y dependencias
# - Instalar Tesseract OCR
# - Configurar entornos virtuales
# - Instalar dependencias del frontend
# - Configurar variables de entorno
```

### **2. Inicio de Servicios**
```powershell
# Inicio automático de todos los servicios
.\start-zatobox.ps1

# Funciones principales:
# - Detener servicios existentes
# - Iniciar Frontend (puerto 5173)
# - Iniciar Backend (puerto 4444)
# - Iniciar OCR Service (puerto 8001)
# - Monitoreo continuo de servicios
```

### **3. Verificación de Instalación**
```powershell
# Verificar que todo esté funcionando
.\verificar-instalacion.ps1

# Verifica:
# - Python instalado
# - Tesseract funcionando
# - Dependencias instaladas
# - Servicios ejecutándose
```

---

## 📊 Métricas y Monitoreo

### **1. Métricas del Sistema**
- **Tiempo de respuesta de API**: < 200ms
- **Precisión del OCR**: > 95% en documentos claros
- **Uptime del sistema**: > 99.9%
- **Capacidad de usuarios concurrentes**: 100+

### **2. Logs y Debugging**
```python
# Backend logging
logging.basicConfig(level=logging.INFO)

# OCR debugging
@app.route('/api/v1/invoice/debug', methods=['GET'])
def get_debug_info():
    return jsonify({
        "tesseract_version": pytesseract.get_tesseract_version(),
        "supported_languages": pytesseract.get_languages(),
        "system_info": platform.platform()
    })
```

### **3. Health Checks**
```python
# Backend health check
@app.get("/health")
def health_check():
    return {"status": "healthy", "timestamp": datetime.now()}

# OCR health check
@app.route('/health', methods=['GET'])
def health_check():
    return jsonify({"status": "healthy", "version": "ultra-improved-v1.0"})
```

---

## 🚨 Manejo de Errores

### **1. Errores del Frontend**
```typescript
// Manejo centralizado de errores
const apiRequest = async <T>(endpoint: string, options: RequestInit = {}): Promise<T> => {
  try {
    const response = await fetch(`${API_BASE_URL}${endpoint}`, config);
    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      throw new Error(errorData.message || `HTTP error! status: ${response.status}`);
    }
    return response.json();
  } catch (error) {
    console.error('API Error:', error);
    throw error;
  }
};
```

### **2. Errores del Backend**
```python
# Manejo de excepciones HTTP
@router.get("/users")
def list_users(current_user=Depends(get_current_user)):
    if not current_user.get("is_admin"):
        raise HTTPException(status_code=403, detail="Access denied")
    return auth_service.get_list_users()
```

### **3. Errores del OCR**
```python
# Manejo de errores en procesamiento OCR
@app.route('/api/v1/invoice/process', methods=['POST'])
def process_invoice():
    try:
        # Procesamiento OCR
        pass
    except Exception as e:
        print(f"Error en procesamiento: {e}")
        traceback.print_exc()
        return jsonify({'error': f'Processing failed: {str(e)}'}), 500
```

<div align="center">

**🗺️ Mapeo Completo del Proyecto ZatoBox v2.0.1**

*Este documento proporciona una visión completa de la arquitectura, conexiones y flujos del sistema*

**Última actualización**: 14 Agosto 2025  
**Versión del documento**: 2.0.1

</div>

