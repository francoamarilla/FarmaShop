# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**FarmaShop / Farmacia Luz** is a pharmacy e-commerce web application (TPI university project). It has two independent parts:
- **Frontend**: Static multi-page HTML/JS/Tailwind CSS site
- **Backend**: ASP.NET Core Web API (`API/` folder)

## Commands

### Frontend – Tailwind CSS
```bash
pnpm run build:styles
# Watches input.css → assets/output.css (must be running when editing HTML/CSS)
```
No build step otherwise. Open HTML files directly in a browser or via a local static server.

### Backend – ASP.NET Core API
```bash
cd API
dotnet run
# Starts at https://localhost:7028
# Swagger UI available at https://localhost:7028/swagger when in Development
```

Database: SQL Server, database name `tpi_farmaciaa`. Connection string uses Windows Integrated Security pointing to the machine `COMPUBALTI` — change `DefaultConnection` in `appsettings.json` to point to a local SQL Server instance. The DB schema is in `scriptActualizadooooo.sql`.

## Architecture

### Frontend

Multi-page app. Every HTML page links `assets/output.css` (compiled Tailwind) and Flowbite via CDN.

**Global shared JS** (`index.js`): Loaded by most pages. Handles:
- Auth state (JWT token stored in `localStorage` as `authToken`)
- Login/logout modal
- User menu (logged-in vs. logged-out state)
- Shopping cart panel (sidebar, checkout flow)
- `cargarMisCompras()` and `cargarMetodosDePago()` — called on pages that have the matching container IDs

**Category pages** (`paginas/`): Each has a corresponding JS file in `assets/js/` (e.g., `perfumes.js`, `bebes.js`) that fetches products by type ID from the API and renders them. Products are added to the cart via `agregarAlCarrito()` defined in `index.js` (it must be loaded first).

**Admin panel** (`admin/`): Restricted to users with `idTipoUsuario === 3`. Uses `assets/js/api.js` (thin fetch wrapper) plus `assets/js/productos.js` and `assets/js/ventas.js` for admin-specific CRUD UI.

**API base URL** is hardcoded in two places:
- `index.js:1` → `const API_BASE_URL = "https://localhost:7028"`
- `assets/js/api.js:1` → `const API_URL = "https://localhost:7028/api"`

### Backend (C#)

Layered architecture: **Controller → Service → Repository → EF Core → SQL Server**

- `Controllers/` — REST endpoints, one per domain entity
- `Services/` — business logic (interfaces in `Services/Interfaces/`, implementations in `Services/Implementations/`)
- `Repositories/` — data access (same pattern)
- `Models/` — EF Core entity classes auto-generated from DB + `FarmaciaContext.cs`
- `DTOs/` — request/response shapes

**Authentication**: JWT Bearer tokens. Login via `POST /api/Login/login` → returns `{ token }`. Subsequent requests carry `Authorization: Bearer <token>`.

**Key API endpoints used by the frontend**:
| Endpoint | Purpose |
|---|---|
| `POST /api/Login/login` | Authenticate, returns JWT |
| `GET /api/Clientes/me` | Current user profile |
| `PUT /api/Clientes/me` | Update profile |
| `GET /api/Suministros` | All products |
| `GET /api/Suministros/tipo/{id}` | Products by category type |
| `GET /api/Carrito/cliente/{userId}` | Get user's cart |
| `POST /api/Carrito` | Create cart |
| `POST /api/Carrito/{cartId}/items` | Add item to cart |
| `PUT /api/Carrito/items/{idItem}` | Update item quantity |
| `DELETE /api/Carrito/items/{idItem}` | Remove item |
| `POST /api/Carrito/{cartId}/checkout` | Confirm purchase |
| `GET /api/Factura` | All invoices |
| `GET /api/Factura/sucursales` | Branch list |
| `GET /api/Factura/formas-pago` | Payment methods |

## UI Conventions

Brand color palette (used as Tailwind arbitrary values):
- `#0e4448` — dark teal (primary dark)
- `#275c74` — medium teal (headers, nav text)
- `#12b1be` — bright teal (CTAs, accent)

Custom utility classes defined inline: `font-family-base` (inherits font for Playfair Display overrides), `styles_li`, `li_titulos`.

The `.history/` folder is VS Code Local History extension output — ignore it.
