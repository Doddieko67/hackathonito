# MexGo — Documento raíz
**Los Mossitos · Genius Arena 2026 · Fundación Coppel**

---

## Qué es esto

Referencia central. Si tomas una decisión técnica, la anotas aquí.
Si ya está resuelta, la mueves al `.md` correspondiente.
Nada se decide en el chat y se pierde.

---

## El proyecto

PWA conversacional con IA que conecta turistas con micronegocios verificados
por Ola México durante el Mundial 2026. Sin descarga — entra por QR.

Diferenciador: algoritmo de equidad que **distribuye** el tráfico turístico
en lugar de concentrarlo. Ver `ALGORITMO.md`.

Para ver con mas detalles y entender mejor el objetivo de este proyecto, 
`PROPUESTAS.md`. Aunque, las funcionalidades y los detalles tecnicos fueron propuestos
al inicio, por lo que, van a cambiar a lo largo del desarrollo, entonces no se debe usar el mencionado
como referencia principal para la toma de decisiones tecnicas, si no meros conceptuales.

### Modelo de usuarios activo

- Turista
- EncargadoDelNegocio
- Admin
- SuperAdmin

La arquitectura y tecnologías se mantienen. El cambio actual es de flujos y permisos (RBAC) en backend, especialmente para autenticación, onboarding, revisión de solicitudes de negocio y operación técnica.

---

## Equipo

| Quién  | Rol            | Qué toca                                              |
|--------|----------------|-------------------------------------------------------|
| Fidel | Arquitecto     | Arquitectura, `lib/equity.ts`, Gemini, deploy AWS     |
| Emi    | Frontend       | Flujo turista, Mapbox, tarjetas, cuestionario         |
| Farid  | Frontend       | Panel admin, subida de fotos                          |
| Alan   | Backend        | Supabase, schema SQL, endpoints, auth                 |
| Xavier | Apoyo backend  | Datos de prueba, testing, documentación               |

> Cada módulo tiene un dueño. No tocas el código de otro sin avisar.
> Contratos entre módulos → `API.md`.
> Se especifica mejor la funcion y el rol de cada uno en `ROLES.md`.

---

## Stack

| Qué        | Tecnología          | Estado                              |
|------------|---------------------|-------------------------------------|
| Framework  | Next.js 15          | ✓ Confirmado                        |
| BD         | Supabase/PostgreSQL | ✓ Confirmado                        |
| IA         | Gemini Flash        | ✓ Confirmado                        |
| Mapas      | Mapbox GL JS        | ✓ Confirmado                        |
| i18n       | i18next + DeepL     | ✓ Confirmado                        |
| Imágenes   | Cloudinary          | ✓ Confirmado                        |
| Estilos    | Tailwind CSS        | ✓ Confirmado                        |
| Lenguaje   | TypeScript estricto | ✓ Confirmado                        |
| Offline    | PWA Cache API       | ✓ Confirmado                        |
| Deploy     | AWS                 | ✓ Confirmado — servicio ? pendiente |

Detalle de decisiones → `ARQUITECTURA.md`.

---

## Documentos

| Archivo                  | Qué tiene                                          |
|--------------------------|----------------------------------------------------|
| `DOCUMENTO.md`           | Este archivo. Raíz.                                |
| `PROPUESTAS.pdf`         | Marco conceptual y objetivos                       |
| `ROLES.md`               | Rol especifico y lo que debe aprender cada uno     |
| `ARQUITECTURA.md`        | Patrones evaluados, patrón elegido, carpetas.      |
| `SCHEMA.md`              | Tablas SQL, relaciones.                            |
| `API.md`                 | Contratos de endpoints: request/response/errores.  |
| `ALGORITMO.md`           | Fórmula del score, pseudocódigo, casos borde.      |
| `IA.md`                  | Gemini, perfil cultural, itinerario.               |
| `FRONTEND.md`            | Compartido: `ui/`, Tailwind, convenciones.         |
| `FRONTEND_TURISTA.md`    | Flujo turista, Mapbox, tarjetas, offline.          |
| `FRONTEND_ADMIN.md`      | Panel, auth, Cloudinary.                           |
| `BUENAS_PRACTICAS.md`    | Naming, commits, PR, qué no hacer.                 |
| `SETUP.md`               | Cómo levantar el proyecto desde cero.              |
| `DECISIONS.md`           | Funcionalidades y decisiones conceptuales          |
| `CONTEXT_ENGINEERING.md` | Cómo usar IA en el proyecto.                       |
| `TECNO_OBLIGATORIAS.md`  | Herramientas, scripts, comandos y todo lo que todos deben saber |


---

## Decisiones cerradas

- → **Mapbox sobre Google Maps** — más personalizable, más barato en free tier.
- → **Patrón: monolito modular + BFF** — ver `ARQUITECTURA.md`.
- → **TypeScript estricto** — sin `any` en ningún lado.
- → **Algoritmo solo en `lib/equity.ts`** — nunca en el cliente.
- → **Sin `NEXT_PUBLIC_` en claves de servidor** — Gemini, Mapbox server y Supabase service role son exclusivos del servidor.
- → **Docs en `.md`** — todo Markdown. PDF desde Pandoc si se necesita entregar.
- → **Modelo RBAC de 4 roles** — Turista, EncargadoDelNegocio, Admin y SuperAdmin.
- → **Workflow de solicitudes de negocio por estados** — `Pendiente`, `En revision`, `Rechazado`, `Aprobado`.

---

## Pendientes

- ? **Servicio AWS exacto** — Amplify / EC2+Nginx / App Runner. (Fidel)
- ? **Contratos de API** — schemas por endpoint antes del día 1. Alan + Fidel → `API.md`.
- ? **Caché del score de saturación** — memoria / Supabase / Redis. Impacta `lib/equity.ts`. (Fidel)
- ? **Definir contratos finales de auth social** — Google/Apple en Supabase Auth y estrategia de linking de cuentas. (Alan)
- ? **Definir plantilla de correos transaccionales** — verificación de cuenta, solicitud recibida, observaciones, aprobación. (Alan + Farid)

---

## Cambios

| Fecha      | Quién  | Qué                                        |
|------------|--------|--------------------------------------------|
| 2026-03-31 | Fidel | v1.0 — documento inicial en `.tex`.        |
| 2026-03-31 | Fidel | v1.1 — nombres reales del equipo.          |
| 2026-03-31 | Fidel | v1.2 — migrado a `.md`, estilo cuaderno.   |
| 2026-04-06 | Alan | v1.3 — se oficializa modelo de usuarios y flujo RBAC en backend. |
