<div align="center">

# 🏨 Guía de Consultas SQL Avanzadas - Bootcamp Tranformación Digital para la Docencia Técnica

**PostgreSQL 18 · pgAdmin 4 · Esquema `tourism`**

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-18-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![pgAdmin](https://img.shields.io/badge/pgAdmin-4-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-20_Consultas-4479A1?style=for-the-badge&logo=databricks&logoColor=white)
![Estado](https://img.shields.io/badge/Estado-Completo-1E7145?style=for-the-badge)
![Licencia](https://img.shields.io/badge/Licencia-MIT-yellow?style=for-the-badge)

<br/>

> Colección de **20 consultas SQL** sobre un sistema de gestión turística —  
> desde inserciones básicas hasta subconsultas avanzadas, documentadas  
> con datos realistas y buenas prácticas de administración de bases de datos.

</div>

---

## 📋 Tabla de contenido

- [Descripción del proyecto](#-descripción-del-proyecto)
- [Herramientas utilizadas](#-herramientas-utilizadas)
- [Estructura del repositorio](#-estructura-del-repositorio)
- [Esquema de la base de datos](#-esquema-de-la-base-de-datos)
- [Guía de consultas](#-guía-de-consultas)
- [Autor](#-autor)

---

## 📌 Descripción del proyecto

Este proyecto documenta el diseño e implementación de una base de datos relacional orientada al sector de **turismo y alojamiento**, junto con una guía completa de 20 consultas SQL organizadas por categoría y complejidad creciente.

### Objetivos

- Modelar entidades reales del ecosistema turístico: propietarios, alojamientos, huéspedes, reservas, pagos y reseñas.
- Demostrar el uso correcto de **DDL, DML, JOINs, funciones de agregación, HAVING y subconsultas** en PostgreSQL.
- Servir como material de referencia académico y profesional para la gestión de bases de datos relacionales.

### Alcance

| Área | Detalle |
|------|---------|
| Motor de base de datos | PostgreSQL 18 |
| Esquema | `tourism` |
| Tablas principales | 11 entidades relacionadas |
| Consultas documentadas | 20 (categorizadas) |
| Datos de ejemplo | Ficticios pero verosímiles |

---

## 🛠️ Herramientas utilizadas

| Herramienta | Versión | Uso |
|-------------|---------|-----|
| ![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-336791?logo=postgresql&logoColor=white) **PostgreSQL** | 18 | Motor de base de datos relacional |
| ![pgAdmin](https://img.shields.io/badge/-pgAdmin_4-336791?logo=postgresql&logoColor=white) **pgAdmin 4** | Latest | IDE gráfico para administración y ejecución de consultas |
| **pgERD** (pgAdmin) | Built-in | Diseño y visualización del diagrama entidad-relación |
| ![Markdown](https://img.shields.io/badge/-Markdown-000000?logo=markdown&logoColor=white) **Markdown** | — | Documentación del proyecto |
| ![GitHub](https://img.shields.io/badge/-GitHub-181717?logo=github&logoColor=white) **GitHub** | — | Control de versiones y publicación |

---

## 📁 Estructura del repositorio

```
ConsultasPostgres/
│
├── 📄 README.md                       ← Este archivo
│
├── 📂 sql/
│   └── ConsultasAvanzadas_Postgres.sql       ← Las 20 consultas documentadas
│
└── 📂 docs/
    └── SQL_Avanzadas.pf      ← Guía de consultas formateada (pf)
```

---

## 🗃️ Esquema de la base de datos

El modelo relacional se compone de **11 tablas** dentro del esquema `tourism`, organizadas en tres grupos funcionales.

### Diagrama de relaciones (ERD)

```
┌──────────────────────┐        ┌─────────────────────────────┐
│        owners        │        │     accommodation_types     │
├──────────────────────┤        ├─────────────────────────────┤
│ PK owner_id          │        │ PK accommodation_type_id    │
│    first_name        │        │    type_name                │
│    last_name         │        │    description              │
│    company_name      │        └──────────────┬──────────────┘
│    email             │                       │ 1
│    phone             │                       │
│    tax_id            │          ┌────────────┴─────────────┐
│    city · country    │          │                          │
└──────────┬───────────┘          ▼ N                        │
           │ 1         ┌──────────────────────────┐         │
           └──────────►│      accommodations      │◄────────┘ N
                    N  ├──────────────────────────┤
                       │ PK accommodation_id      │
                       │ FK owner_id              │
                       │ FK accommodation_type_id │
                       │ FK location_id           │
                       │    name                  │
                       │    base_price_per_night  │
                       │    max_guests            │
                       │    bedroom_count         │
                       │    bathroom_count        │
                       │    check_in_time         │
                       │    check_out_time        │
                       │    is_active   BOOLEAN   │
                       └────────────┬─────────────┘
                                    │ 1
                     ┌──────────────┼──────────────┐
                     │              │              │
                     ▼ N            ▼ N            ▼ N
            ┌─────────────────┐ ┌────────┐ ┌───────────────┐
            │    bookings     │ │ rooms  │ │    reviews    │
            ├─────────────────┤ ├────────┤ ├───────────────┤
            │ PK booking_id   │ │ PK room_id│ PK review_id │
            │ FK guest_id     │ │ FK accom_id│ FK booking_id│
            │ FK accom_id     │ │ room_name│ FK guest_id   │
            │ FK room_id      │ │ room_code│ FK accom_id   │
            │ FK booking_     │ │ capacity │    rating     │
            │    status_id    │ │ bed_count│ review_title  │
            │ check_in_date   │ │is_avail. │ review_text   │
            │ check_out_date  │ └────────┘ │ review_date   │
            │ total_amount    │            └───────────────┘
            │ booking_ref     │
            └────────┬────────┘
                     │ 1
          ┌──────────┴───────────┐
          │                      │
          ▼ N                    ▼ N
  ┌────────────────┐   ┌──────────────────────┐
  │    payments    │   │    booking_guests    │
  ├────────────────┤   ├──────────────────────┤
  │ PK payment_id  │   │ PK booking_guest_id  │
  │ FK booking_id  │   │ FK booking_id        │
  │    amount      │   │    first_name        │
  │ payment_method │   │    last_name         │
  │ payment_status │   │    age               │
  │ payment_date   │   │    document_number   │
  └────────────────┘   └──────────────────────┘

  ┌──────────────────┐   ┌─────────────────────────┐
  │      guests      │   │    booking_statuses     │
  ├──────────────────┤   ├─────────────────────────┤
  │ PK guest_id      │   │ PK booking_status_id    │
  │    first_name    │   │    status_name          │
  │    last_name     │   │    description          │
  │    email         │   └─────────────────────────┘
  │    phone         │
  │    date_of_birth │   ┌─────────────────────────┐
  │    nationality   │   │        locations        │
  │    passport_num  │   ├─────────────────────────┤
  └──────────────────┘   │ PK location_id          │
                         │    country · state      │
  ┌──────────────────┐   │    city · district      │
  │    amenities     │   │    address_line1        │
  ├──────────────────┤   │    postal_code          │
  │ PK amenity_id    │   │    latitude · longitude │
  │    amenity_name  │   └─────────────────────────┘
  │    description   │
  └──────────────────┘
  (vinculada a accommodations
   vía accommodation_amenities)
```

## 📚 Guía de consultas

Las 20 consultas están organizadas en **7 categorías** de complejidad creciente:

| # | Categoría | Operación | Técnica utilizada |
|---|-----------|-----------|-------------------|
| 01 | `INSERT` | Agregar propietario | Inserción simple en `owners` |
| 02 | `INSERT` | Agregar alojamiento | INSERT con múltiples FK |
| 03 | `INSERT` | Huésped y reserva | `BEGIN / COMMIT` + `currval()` |
| 04 | `INSERT` | Agregar pago | `CURRENT_TIMESTAMP` en `payments` |
| 05 | `SELECT` | Alojamientos activos | `WHERE is_active = TRUE` |
| 06 | `SELECT` | Huéspedes por país | Filtro por `nationality` |
| 07 | `SELECT` | Reservas por fechas | `BETWEEN` sobre `check_in_date` |
| 08 | `UPDATE` | Actualizar precio | `SET base_price_per_night` |
| 09 | `UPDATE` | Estado de reserva | Actualizar `booking_status_id` |
| 10 | `DELETE` | Eliminar reseña | `DELETE WHERE review_id` |
| 11 | `JOIN` | Reservas + huésped | `INNER JOIN bookings → guests` |
| 12 | `JOIN` | Alojamiento completo | `INNER JOIN` en 4 tablas |
| 13 | `JOIN` | Pagos + reservas | JOIN encadenado `payments → bookings → guests` |
| 14 | `LEFT JOIN` | Sin reseñas | `IS NULL` sobre `reviews` |
| 15 | `LEFT JOIN` | Sin reservas | `IS NULL` sobre `bookings` |
| 16 | `AGG` | Total de ingresos | `SUM / AVG / MIN / MAX` sobre `payments` |
| 17 | `AGG` | Promedio de rating | `AVG(rating)` + `GROUP BY` |
| 18 | `AGG` | Top alojamientos | `COUNT` + `LIMIT 5` |
| 19 | `HAVING` | Más de 3 reservas | `GROUP BY` + `HAVING COUNT > 3` |
| 20 | `Subconsulta` | Alojamiento más caro | `WHERE` + `SELECT MAX()` |

---

### Requisitos previos

- PostgreSQL 18 instalado
- pgAdmin 4 configurado
- Esquema `tourism` con todas sus tablas ya creadas

---

## 👨‍💻 Autor

<div align="center">

**Alan Wilner Moris Fermán** — Docente de Educación Media  
Instituto Nacional Profesor Francisco Venura zelaya · Ciencias de la Computación y Bachillerato Técnico Vocacional en Desarrollo de Software

*Administración de Bases de Datos · PostgreSQL · Desarrollo Web*

</div>

---

<div align="center">

*Desarrollado con fines Educativos — Ministerio de Educación Ciencia y Técnologia de El Salvador, 2026*

</div>