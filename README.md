# BackendC - API REST de Gestión Bancaria

## 📋 Descripción

**BackendC** es una API REST educativa desarrollada con ASP.NET Core 6.0 que implementa un sistema completo de gestión bancaria. Proporciona endpoints para administrar clientes, cuentas bancarias y movimientos de transacciones (depósitos/retiros), con generación de reportes por cliente y rango de fechas. Utiliza Entity Framework Core con SQL Server para persistencia de datos y sigue una arquitectura de capas limpia.

## 🚀 Tipo de Proyecto

**API REST Backend** - Sistema de Gestión Bancaria (Proyecto Educativo)

## 🛠️ Tecnologías Utilizadas

- **ASP.NET Core 6.0** - Framework web principal
- **C# 10** - Lenguaje de programación
- **Entity Framework Core 6.0.8** - ORM para acceso a datos
- **SQL Server 2019+** - Base de datos relacional
- **Swagger/Swashbuckle 6.2.3** - Documentación interactiva de API

## 📚 Frameworks y Librerías

### Backend
- **ASP.NET Core 6.0** - Framework MVC/API
- **Entity Framework Core 6.0.8** - ORM
  - `Microsoft.EntityFrameworkCore.SqlServer`
  - `Microsoft.EntityFrameworkCore.Tools`

### Testing
- **MSTest 2.2.8** - Framework de pruebas unitarias
- **Entity Framework InMemory 6.0.8** - BD en memoria para tests
- **coverlet.collector 3.1.0** - Cobertura de código

### Documentación
- **Swashbuckle.AspNetCore 6.2.3** - Swagger/OpenAPI

### Herramientas
- **Visual Studio 2022** - IDE de desarrollo
- **Docker** - Containerización

## 🏗️ Arquitectura

### Patrón: MVC con Separación de Capas

```
┌─────────────────────────────────────┐
│    Cliente (Frontend/Postman)       │
└────────────────┬────────────────────┘
                 │ HTTP/JSON
                 ↓
┌────────────────┴────────────────────┐
│    Controllers (API Endpoints)      │
│  Clientes | Cuentas | Movimientos   │
│  Reportes                            │
└────────────────┬────────────────────┘
                 │
┌────────────────┴────────────────────┐
│    Models (Entidades de Dominio)    │
│  Personas | Clientes | Cuentas      │
│  Movimientos                         │
└────────────────┬────────────────────┘
                 │
┌────────────────┴────────────────────┐
│    DbContext (Entity Framework)     │
│    ejercicio1Context                │
└────────────────┬────────────────────┘
                 │
┌────────────────┴────────────────────┐
│         SQL Server Database         │
│    (Base de datos: ejercicio1)      │
└─────────────────────────────────────┘
```

### Estructura de Respuesta Unificada

Todas las respuestas API utilizan el modelo `Retorno`:
```json
{
  "procesoCorrecto": true/false,
  "retorno": {
    // datos o mensaje de error
  }
}
```

## 📁 Estructura del Proyecto

```
backendc-/
├── ejercicio1/                   # Proyecto principal ASP.NET Core
│   ├── Controllers/
│   │   ├── ClientesController.cs      # CRUD de clientes (5 métodos)
│   │   ├── CuentasController.cs       # CRUD de cuentas (5 métodos)
│   │   ├── MovimientosController.cs   # CRUD de movimientos (5 métodos)
│   │   ├── ReportesController.cs      # Reportes por cliente
│   │   └── WeatherForecastController.cs # Ejemplo (eliminar en producción)
│   │
│   ├── Models/
│   │   ├── Personas.cs               # Entidad Personas
│   │   ├── Clientes.cs               # Entidad Clientes (hereda de Personas)
│   │   ├── Cuentas.cs                # Entidad Cuentas bancarias
│   │   ├── Movimientos.cs            # Entidad Movimientos
│   │   ├── ejercicio1Context.cs      # DbContext EF Core (157 líneas)
│   │   └── BaseResponse.cs           # Modelo Retorno
│   │
│   ├── Core/                         # Lógica de negocio (vacío)
│   │   └── MovimientoCore.cs
│   │
│   ├── Program.cs                    # Configuración ASP.NET Core
│   ├── Dockerfile                    # Multi-stage Docker build
│   ├── ejercicio1.csproj             # Definición del proyecto
│   ├── appsettings.json              # Configuración (connection string)
│   ├── appsettings.Development.json  # Config desarrollo
│   └── libman.json                   # Client-side libraries
│
├── TestProject/                      # Proyecto de pruebas unitarias
│   ├── BasePruebas.cs
│   ├── PruebasUnitarias/
│   ├── TestProject.csproj
│   └── Usings.cs
│
├── ejercicio1.sln                    # Solución Visual Studio
├── .dockerignore
└── .vs/                              # Carpeta Visual Studio (metadata)
```

## ✨ Características Principales

### 👥 Gestión de Clientes

**Endpoints (5 métodos):**
- `GET /clientes` - Listar todos los clientes
- `GET /clientes/{id}` - Obtener cliente por ID
- `POST /clientes` - Crear nuevo cliente (incluye persona asociada)
- `PUT /clientes/{id}` - Actualizar cliente
- `DELETE /clientes/{id}` - Eliminar cliente y persona

**Características:**
- Relación 1:1 con Personas (cada cliente es una persona)
- Campos: IdPersona (FK), Clave (contraseña), Estado
- Validación de existencia antes de operaciones
- Eliminación en cascada de persona asociada

### 💰 Gestión de Cuentas Bancarias

**Endpoints (5 métodos):**
- `GET /cuentas` - Listar todas las cuentas
- `GET /cuentas/{id}` - Obtener cuenta por ID
- `POST /cuentas` - Crear nueva cuenta
- `PUT /cuentas/{id}` - Actualizar cuenta
- `DELETE /cuentas/{id}` - Eliminar cuenta

**Características:**
- Relación N:1 con Clientes (un cliente puede tener múltiples cuentas)
- Campos: NumeroCuenta, TipoCuenta, SaldoInicial, Estado
- Tipos de cuenta: Ahorro, Corriente
- Control de saldo inicial
- Estado activo/inactivo

### 📊 Gestión de Movimientos Bancarios

**Endpoints (5 métodos):**
- `GET /movimientos` - Listar todos los movimientos
- `GET /movimientos/{id}` - Obtener movimiento por ID
- `POST /movimientos` - Crear movimiento (depósito/retiro)
- `PUT /movimientos/{id}` - Actualizar movimiento
- `DELETE /movimientos/{id}` - Eliminar movimiento

**Características:**
- Relación N:1 con Cuentas
- Cálculo automático de saldo acumulativo
- Tipos: Depósito (+), Retiro (-)
- Campos: Fecha, TipoMovimiento, Valor, Saldo
- Validación de saldo suficiente para retiros
- Registro cronológico de transacciones

### 📈 Reportes de Estado de Cuenta

**Endpoint:**
- `GET /reportes/{cliente}/{fecha}` - Movimientos por cliente y rango

**Formato del parámetro fecha:**
```
/reportes/1/2024-01-01%2024-12-31
```
(URL encode: `fechaInicio%fechaFin`)

**Respuesta incluye:**
- Datos del cliente
- Cuentas asociadas
- Movimientos en el rango de fechas
- Saldo actual de cada cuenta

## 🔧 Instalación

### Prerrequisitos

- .NET 6.0 SDK o superior
- SQL Server 2019+ (Express, Developer o Enterprise)
- Visual Studio 2022 o VS Code
- SQL Server Management Studio (opcional)

### Pasos de Instalación

1. **Clonar el repositorio**
```bash
git clone https://github.com/dannyggg3/backendc-.git
cd backendc-
```

2. **Abrir solución en Visual Studio**
```bash
start ejercicio1.sln
```

O desde línea de comandos:
```bash
cd ejercicio1
dotnet restore
```

3. **Configurar cadena de conexión**

Editar `appsettings.json`:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=192.168.0.105;Database=ejercicio1;User Id=sa;Password=TU_PASSWORD;TrustServerCertificate=True"
  }
}
```

**Importante:** Cambiar servidor, usuario y contraseña según tu configuración.

4. **Crear base de datos**

Opción A - Migrations (si existen):
```bash
dotnet ef database update
```

Opción B - Script SQL manual:
```sql
CREATE DATABASE ejercicio1;
GO

USE ejercicio1;
GO

-- Las tablas se crean automáticamente al ejecutar la app
-- gracias a EF Core
```

5. **Compilar el proyecto**
```bash
dotnet build
```

6. **Ejecutar la aplicación**
```bash
dotnet run
```

La API estará disponible en:
- `https://localhost:7108` (HTTPS)
- `http://localhost:5108` (HTTP)

7. **Acceder a Swagger**
```
https://localhost:7108/swagger
```

## 💻 Uso

### Documentación Interactiva Swagger

Navega a `https://localhost:7108/swagger` para:
- Ver todos los endpoints disponibles
- Probar la API directamente desde el navegador
- Ver modelos de datos
- Generar ejemplos de request/response

### Ejemplo 1: Crear Cliente

**Request:**
```bash
curl -X POST https://localhost:7108/clientes \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Juan Pérez",
    "genero": "M",
    "edad": 35,
    "identificacion": "1234567890",
    "direccion": "Av. Principal 123",
    "telefono": "0991234567",
    "clave": "password123",
    "estado": true
  }'
```

**Response:**
```json
{
  "procesoCorrecto": true,
  "retorno": {
    "idCliente": 1,
    "idPersona": 1,
    "clave": "password123",
    "estado": true,
    "idPersonaNavigation": {
      "idPersona": 1,
      "nombre": "Juan Pérez",
      "genero": "M",
      "edad": 35,
      "identificacion": "1234567890",
      "direccion": "Av. Principal 123",
      "telefono": "0991234567"
    }
  }
}
```

### Ejemplo 2: Crear Cuenta Bancaria

**Request:**
```bash
curl -X POST https://localhost:7108/cuentas \
  -H "Content-Type: application/json" \
  -d '{
    "numeroCuenta": "1001234567",
    "tipoCuenta": "Ahorro",
    "saldoInicial": 1000.00,
    "estado": true,
    "idCliente": 1
  }'
```

### Ejemplo 3: Registrar Movimiento (Depósito)

**Request:**
```bash
curl -X POST https://localhost:7108/movimientos \
  -H "Content-Type: application/json" \
  -d '{
    "idCuenta": 1,
    "fecha": "2024-11-14T10:30:00",
    "tipoMovimiento": "Depósito",
    "valor": 500.00,
    "saldo": 1500.00
  }'
```

### Ejemplo 4: Obtener Reporte de Cliente

**Request:**
```bash
curl -X GET "https://localhost:7108/reportes/1/2024-01-01%2024-12-31"
```

**Response:**
```json
{
  "procesoCorrecto": true,
  "retorno": [
    {
      "fecha": "2024-11-14",
      "cliente": "Juan Pérez",
      "numeroCuenta": "1001234567",
      "tipo": "Ahorro",
      "saldoInicial": 1000.00,
      "estado": true,
      "movimiento": 500.00,
      "saldoDisponible": 1500.00
    }
  ]
}
```

## 🗄️ Modelo de Datos

### Diagrama de Relaciones

```
┌─────────────────┐
│    Personas     │
│─────────────────│
│ IdPersona (PK)  │
│ Nombre          │
│ Genero          │
│ Edad            │
│ Identificacion  │
│ Direccion       │
│ Telefono        │
└────────┬────────┘
         │ 1
         │
         │ 1
┌────────┴────────┐
│    Clientes     │
│─────────────────│
│ IdCliente (PK)  │
│ IdPersona (FK)  │
│ Clave           │
│ Estado          │
└────────┬────────┘
         │ 1
         │
         │ N
┌────────┴────────┐
│     Cuentas     │
│─────────────────│
│ IdCuenta (PK)   │
│ NumeroCuenta    │
│ TipoCuenta      │
│ SaldoInicial    │
│ Estado          │
│ IdCliente (FK)  │
└────────┬────────┘
         │ 1
         │
         │ N
┌────────┴────────┐
│   Movimientos   │
│─────────────────│
│ IdMovimiento(PK)│
│ IdCuenta (FK)   │
│ Fecha           │
│ TipoMovimiento  │
│ Valor           │
│ Saldo           │
└─────────────────┘
```

### Entidades

**Personas:**
- PK: `IdPersona`
- Datos personales básicos
- Relación 1:1 con Clientes

**Clientes:**
- PK: `IdCliente`
- FK: `IdPersona` → Personas
- Campos adicionales: Clave, Estado
- Relación 1:N con Cuentas

**Cuentas:**
- PK: `IdCuenta`
- FK: `IdCliente` → Clientes
- Tipos: Ahorro, Corriente
- Relación 1:N con Movimientos

**Movimientos:**
- PK: `IdMovimiento`
- FK: `IdCuenta` → Cuentas
- Tipos: Depósito (+), Retiro (-)
- Saldo calculado acumulativamente

## 🔌 API Endpoints Completos

### Clientes

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/clientes` | Listar todos |
| GET | `/clientes/{id}` | Obtener por ID |
| POST | `/clientes` | Crear |
| PUT | `/clientes/{id}` | Actualizar |
| DELETE | `/clientes/{id}` | Eliminar |

### Cuentas

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/cuentas` | Listar todas |
| GET | `/cuentas/{id}` | Obtener por ID |
| POST | `/cuentas` | Crear |
| PUT | `/cuentas/{id}` | Actualizar |
| DELETE | `/cuentas/{id}` | Eliminar |

### Movimientos

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/movimientos` | Listar todos |
| GET | `/movimientos/{id}` | Obtener por ID |
| POST | `/movimientos` | Crear |
| PUT | `/movimientos/{id}` | Actualizar |
| DELETE | `/movimientos/{id}` | Eliminar |

### Reportes

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/reportes/{cliente}/{fecha}` | Movimientos por cliente y rango |

## 📊 Estadísticas del Proyecto

| Métrica | Valor |
|---------|-------|
| Controladores | 5 (4 funcionales + 1 ejemplo) |
| Métodos API | 16 endpoints REST |
| Modelos (Entidades) | 4 principales |
| Líneas DbContext | 157 |
| Versión .NET | 6.0 |
| Framework EF Core | 6.0.8 |

## 🐳 Docker

### Build de Imagen

```bash
docker build -t backendc-api:latest -f ejercicio1/Dockerfile .
```

### Ejecutar Contenedor

```bash
docker run -d -p 5108:80 \
  -e ConnectionStrings__DefaultConnection="Server=host.docker.internal;Database=ejercicio1;User Id=sa;Password=PASSWORD;TrustServerCertificate=True" \
  backendc-api:latest
```

### Docker Compose

Crear `docker-compose.yml`:
```yaml
version: '3.8'
services:
  api:
    build:
      context: .
      dockerfile: ejercicio1/Dockerfile
    ports:
      - "5108:80"
    environment:
      - ConnectionStrings__DefaultConnection=Server=sqlserver;Database=ejercicio1;User Id=sa;Password=YourStrong!Passw0rd
    depends_on:
      - sqlserver

  sqlserver:
    image: mcr.microsoft.com/mssql/server:2019-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=YourStrong!Passw0rd
    ports:
      - "1433:1433"
```

Ejecutar:
```bash
docker-compose up -d
```

## 🧪 Testing

### Ejecutar Tests Unitarios

```bash
cd TestProject
dotnet test
```

### Con Cobertura de Código

```bash
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover
```

### Tests Específicos

```bash
dotnet test --filter "FullyQualifiedName~PruebasUnitarias"
```

## 🔒 Seguridad

### Consideraciones

⚠️ Este es un proyecto educativo. Para producción considerar:

- [ ] Implementar autenticación (JWT, OAuth)
- [ ] Agregar autorización basada en roles
- [ ] Validación de entrada más estricta
- [ ] Rate limiting
- [ ] HTTPS obligatorio
- [ ] Encriptación de contraseñas (actualmente en texto plano)
- [ ] Logs de auditoría
- [ ] Manejo de errores más robusto
- [ ] Protección contra SQL Injection (EF Core ya protege)

## 🚀 Mejoras Futuras

- [ ] Implementar autenticación JWT
- [ ] Agregar paginación en listados
- [ ] Implementar filtros de búsqueda
- [ ] Agregar validaciones de negocio (ej: saldo insuficiente)
- [ ] Implementar patrón Repository
- [ ] Agregar capa de servicios
- [ ] Implementar Unit of Work
- [ ] Agregar logging estructurado (Serilog)
- [ ] Implementar caché con Redis
- [ ] Agregar versionado de API

## 🛠️ Troubleshooting

| Problema | Solución |
|----------|----------|
| Error de conexión SQL Server | Verificar cadena de conexión en appsettings.json |
| Puerto en uso | Cambiar puerto en launchSettings.json |
| Swagger no carga | Verificar que estés en modo Development |
| Migrations no funcionan | Instalar dotnet-ef: `dotnet tool install --global dotnet-ef` |

## 📄 Licencia

Este proyecto es parte del portafolio de desarrollo de dannyggg3.

## 👤 Autor

**dannyggg3**
- GitHub: [@dannyggg3](https://github.com/dannyggg3)

---

⭐ Si este proyecto te fue útil, considera darle una estrella
