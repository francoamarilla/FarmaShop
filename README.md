# Farmacia Luz – E-commerce Web App

Aplicación web de e-commerce para una farmacia, desarrollada como proyecto integrador (TPI). Permite a los clientes navegar el catálogo por categorías, agregar productos al carrito y finalizar compras. Incluye panel de administración para gestión de productos, clientes y ventas.

## Stack

| Capa | Tecnología |
|------|-----------|
| Frontend | HTML5, Tailwind CSS v4, Flowbite, JavaScript vanilla |
| Backend | ASP.NET Core 8 Web API (C#) |
| Base de datos | SQL Server |
| Autenticación | JWT Bearer |
| Package manager | pnpm |

## Funcionalidades

- Catálogo de productos por categoría (Perfumes, Cuidado Personal, Dermocosmética, Medicamentos, Bebés)
- Registro e inicio de sesión con JWT
- Carrito de compras persistente por usuario
- Checkout con selección de sucursal y método de pago
- Historial de compras y métodos de pago del usuario
- Panel de administración (productos, clientes, ventas) — solo rol administrador

## Cómo ejecutar

### Requisitos

- [.NET 8 SDK](https://dotnet.microsoft.com/download)
- [SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) (o SQL Server Express)
- [pnpm](https://pnpm.io/installation)
- Navegador moderno

### 1. Base de datos

Importar el script SQL en SQL Server Management Studio:

```
database.sql
```

### 2. Backend

```bash
cd API
# Ajustar la connection string en appsettings.json si es necesario
dotnet run
# API disponible en https://localhost:7028
# Swagger UI en https://localhost:7028/swagger
```

### 3. Frontend – compilar CSS

```bash
pnpm install
pnpm run build:styles   # queda en modo watch
```

### 4. Abrir el sitio

Abrir `index.html` en el navegador (o servir con Live Server / cualquier servidor estático).

> **Nota:** la API corre en `https://localhost:7028`. Si cambiás el puerto, actualizá `index.js` línea 1 y `assets/js/api.js` línea 1.

## Estructura del proyecto

```
FarmaShop/
├── index.html          # Página principal
├── index.js            # Lógica global: auth, carrito, menú de usuario
├── registro.html/js    # Registro de nuevos usuarios
├── perfil.html         # Perfil del usuario autenticado
├── misCompras.html     # Historial de compras
├── metodosPagos.html   # Métodos de pago usados
├── paginas/            # Páginas por categoría de producto
├── admin/              # Panel de administración (rol 3)
├── assets/
│   ├── js/             # JS por página (api.js, perfumes.js, bebes.js…)
│   ├── img/            # Imágenes y logos
│   └── output.css      # CSS compilado (no editar directo)
├── input.css           # Entrada de Tailwind
├── API/                # Proyecto ASP.NET Core
│   ├── Controllers/    # Endpoints REST
│   ├── Services/       # Lógica de negocio
│   ├── Repositories/   # Acceso a datos (EF Core)
│   ├── Models/         # Entidades y DbContext
│   └── DTOs/           # Objetos de transferencia
└── database.sql  # Script de base de datos
```

## Arquitectura del backend

Patrón en capas: **Controller → Service → Repository → EF Core → SQL Server**

Cada entidad sigue el mismo flujo. Los servicios implementan interfaces, lo que facilita el reemplazo de implementaciones o la escritura de tests unitarios.

## Autenticación y roles

- Login retorna un JWT almacenado en `localStorage`
- Rol `idTipoUsuario = 3` habilita el acceso al panel de administración
- Rutas del backend protegidas con `[Authorize]`

## Autor

Franco Amarilla — [franco24maxi@gmail.com](mailto:franco24maxi@gmail.com)
