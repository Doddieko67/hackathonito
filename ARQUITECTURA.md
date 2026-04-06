# MexGo — Arquitectura
**Los Mossitos · Genius Arena 2026**

Decisiones de arquitectura. Qué se evaluó, qué se eligió, por qué.
Actualizar cuando cambie algo. Responsable: Fidel.

---

## Patrones evaluados

**Monolito modular — ✗ descartado como patrón puro**
Todo en un repo Next.js. Rápido de arrancar, fácil de desplegar. Pero con 5 personas sin separación clara, los conflictos de git matan el hackathon y el algoritmo de equidad termina mezclado con React. → Se adopta solo la ventaja del repo único.

**Cliente / API separados — ✗ descartado como implementación**
Frontend y backend en proyectos distintos. Separación limpia, pero en 2 días el setup de CORS, dos repos y contratos de API come tiempo real. Si el backend no levanta, el frontend no prueba nada. → Se adopta como mentalidad, no como implementación.

**BFF — Backend for Frontend — ✓ elegido como mentalidad**
Un intermediario orquesta Gemini + Supabase + algoritmo y devuelve al frontend el resultado listo. El frontend solo renderiza. El algoritmo nunca llega al navegador. Requiere definir contratos de API el día 1. → Implementado con Next.js API Routes, no como servicio separado.

**Serverless / Supabase Edge Functions — ✗ descartado para hackathon**
Funciones Deno en la infra de Supabase. Escalan solas, sin servidor propio. Deno es casi idéntico a Node.js pero las diferencias en imports y el cold start de 1–2s añaden fricción innecesaria. → Ruta de migración natural post-hackathon.

**Microservicios — ✗ descartado**
Un servicio por dominio, cada uno con su BD. Escalable y tolerante a fallos, pero service discovery + load balancing + trazabilidad distribuida para 5 personas en 2 días es absurdo. → No aplica.

**Clean Architecture — ~ principio adoptado**
La lógica de negocio no conoce a Supabase ni a Gemini. Elegante y testeable, pero el boilerplate de interfaces y capas formales no cabe en 2 días. → El algoritmo y el perfil cultural son funciones puras en `lib/` sin imports de proveedores.

**Arquitectura hexagonal — ~ principio adoptado**
Puertos y adaptadores. Cambiar Gemini por OpenAI no toca la lógica de negocio. Misma ventaja que Clean, mismo problema de boilerplate. → Gemini, Mapbox y Cloudinary solo se acceden desde `lib/`. Si cambia un proveedor, solo cambia ese archivo.

---

## Patrón elegido

**✓ Monolito modular con mentalidad BFF — Next.js API Routes.**

Un repo. Las API Routes son el BFF: reciben peticiones del frontend, llaman a Gemini y Supabase, aplican el algoritmo, devuelven tarjetas listas. El frontend solo renderiza. La lógica de negocio vive en `lib/` como funciones puras en TypeScript, sin depender de Next.js ni de ningún proveedor.

Por qué este: el equipo ya sabe Next.js, un repo elimina coordinación de servicios, las API Routes protegen el algoritmo del cliente, y post-hackathon se puede migrar a Edge Functions sin tocar el frontend.

### Reglas — no se rompen

- Frontend no importa Supabase directo — solo consume `/app/api/`.
- Algoritmo solo en `lib/equity.ts` — nunca en un componente React.
- Claves de Gemini, Mapbox server y Supabase service role — nunca en `NEXT_PUBLIC_`.
- Cada archivo en `lib/` — una sola responsabilidad.

---

## Modelo de usuarios y control de acceso (backend)

Roles activos del sistema:

| Rol | Autenticación | Permisos principales |
|---|---|---|
| Turista | Email+password, Google, Apple + verificación por correo | Perfil, cuestionario, generación/edición/guardado de itinerario, descarga PDF |
| EncargadoDelNegocio | Registro de solicitud + correo de seguimiento | Alta de solicitud de negocio, correcciones si hay retroalimentación, edición limitada tras aprobación |
| Admin | Inicio de sesión con rol administrativo | Toma de solicitud, revisión, bloqueo por estado, aprobar/rechazar con retroalimentación |
| SuperAdmin | Inicio de sesión con rol técnico | Atención de tickets, supervisión técnica, métricas y operación del sistema |

### Reglas RBAC mínimas

- El backend valida rol en cada endpoint con claims del token y/o tablas de roles.
- Ningún rol puede escribir campos de otro dominio (ejemplo: Turista no modifica solicitudes de negocio).
- Toda transición de estado de solicitud se registra con `updated_by`, `updated_at` y comentario.
- Las acciones administrativas deben generar auditoría (quién, cuándo, desde qué estado a cuál).

### Flujo Turista (backend)

1. Registro: email/password o proveedor social (Google/Apple).
2. Campos obligatorios de onboarding: idioma y país de origen.
3. Verificación por correo antes de habilitar sesión completa.
4. Cuestionario interactivo para perfilado.
5. Generación de itinerario automático y persistencia.
6. Operaciones posteriores: guardar, regenerar, editar (agregar/quitar elementos), descargar PDF y edición de perfil.

### Flujo EncargadoDelNegocio (backend)

1. Envía solicitud de negocio con formulario completo (21 campos definidos por producto).
2. Estado inicial: `Pendiente`.
3. Recibe correo de "solicitud recibida y pendiente de revisión".
4. Si Admin rechaza con observaciones: vuelve a `Pendiente` tras corrección del solicitante.
5. Si Admin aprueba: recibe correo de acceso y se habilita su portal de perfil de negocio con edición limitada.

### Flujo Admin (backend)

Estados de solicitud: `Pendiente`, `En revision`, `Rechazado`, `Aprobado`.

- Al abrir una solicitud en `Pendiente`, transición automática a `En revision`.
- Mientras esté en `En revision`, la solicitud queda bloqueada para otros admins.
- Resultado de revisión:
	- `Aprobado` + notificación de acceso al EncargadoDelNegocio.
	- `Rechazado` + retroalimentación obligatoria para corrección.

### Flujo SuperAdmin (backend)

- Gestión operativa de tickets técnicos del sistema.
- Visibilidad transversal para diagnóstico (logs, métricas, errores de integración).
- No participa en la revisión funcional diaria de solicitudes salvo incidencia.

---

## Estructura de carpetas

```
mexgo/
├── app/
│   ├── (turista)/
│   │   ├── page.tsx              # Bienvenida
│   │   ├── cuestionario/
│   │   ├── itinerario/
│   │   └── mapa/
│   ├── (admin)/
│   │   ├── login/
│   │   ├── dashboard/
│   │   └── negocio/[id]/
│   └── api/                      # BFF — nunca llamado directo desde cliente
│       ├── recomendar/           # POST: perfil → tarjetas
│       ├── itinerario/           # POST: preferencias → itinerario
│       ├── negocios/             # CRUD negocios
│       └── auth/
│
├── lib/                          # Lógica pura — sin deps de Next.js
│   ├── equity.ts                 # Algoritmo de equidad
│   ├── cultural.ts               # Perfil cultural por país
│   ├── gemini.ts                 # Wrapper Gemini
│   ├── mapbox.ts                 # Wrapper Mapbox
│   ├── supabase.ts               # Cliente servidor
│   └── supabase-client.ts        # Cliente browser (solo auth)
│
├── components/
│   ├── ui/                       # Compartido — ver FRONTEND.md
│   ├── turista/
│   └── admin/
│
├── docs/                         # Todos los .md del proyecto
├── supabase/migrations/
├── .env.local                    # Nunca en git
├── .env.example                  # Sí en git — sin valores reales
└── next.config.ts
```

---

## Flujo principal

1. Turista abre PWA por QR → Next.js detecta idioma con `Accept-Language`.
2. Registro/login (email, Google o Apple) + verificación por correo.
3. Onboarding pide idioma y país de origen.
4. Cuestionario → `POST /api/recomendar` con perfil básico.
5. API Route → `lib/cultural.ts` (parámetros del país) → `lib/equity.ts` (score con negocios de Supabase) → 4–6 tarjetas.
6. Frontend renderiza. No procesa nada.
7. Si tiene boleto → `POST /api/itinerario` → Gemini integra el partido al plan del día.
8. Itinerario persistido y editable + descarga PDF.

Flujo paralelo de negocio: alta de solicitud (`Pendiente`) → toma por Admin (`En revision`, bloqueo) → resultado (`Aprobado` o `Rechazado`) con notificación por correo.

---

## Responsables

| Módulo            | Quién  | Archivos                                        |
|-------------------|--------|-------------------------------------------------|
| Arquitectura + deploy | Fidel | `next.config.ts`, AWS, `.env`               |
| Algoritmo         | Fidel | `lib/equity.ts`, `lib/cultural.ts`              |
| Gemini            | Fidel | `lib/gemini.ts`, `app/api/itinerario/`          |
| Frontend turista  | Emi    | `app/(turista)/`, `components/turista/`         |
| Frontend admin    | Farid  | `app/(admin)/`, `components/admin/`             |
| Backend / Supabase| Alan   | `app/api/`, `lib/supabase.ts`, migraciones, RBAC, workflow de solicitudes |
| Apoyo / QA        | Xavier | datos de prueba, tests                          |

---

## Pendientes

- ? **Servicio AWS exacto** — Amplify / EC2+Nginx / App Runner. (Fidel)
- ? **Contratos de API** — schemas por endpoint antes del día 1. Alan + Fidel → `API.md`.
- ? **Caché del score de saturación** — memoria / Supabase / Redis. Impacta `lib/equity.ts`. (Fidel)

---

## Cambios

| Fecha      | Quién  | Qué                                          |
|------------|--------|----------------------------------------------|
| 2026-03-31 | Fidel | v1.0 — evaluación de patrones, decisión inicial. |
| 2026-03-31 | Fidel | v1.1 — nombres reales, párrafos cortos.      |
| 2026-03-31 | Fidel | v1.2 — migrado a `.md`, estilo cuaderno.     |
| 2026-04-06 | Alan | v1.3 — modelo de usuarios, RBAC y flujo de solicitudes por estados. |
