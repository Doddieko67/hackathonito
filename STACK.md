# MexGo — Stack & Referencia Visual
**Los Mossitos · Genius Arena 2026**

Guía visual de referencia rápida para todo el equipo.

---

## Stack tecnológico

| Qué | Tecnología | Quién la toca |
|-----|-----------|---------------|
| Framework | Next.js 16 | Todos |
| Base de datos | Supabase / PostgreSQL | Alan |
| IA | Gemini 2.5 Flash | Fidel |
| Mapas | Mapbox GL JS | Emi |
| Traducciones | i18next + DeepL | Emi |
| Imágenes | Cloudinary | Farid |
| Estilos | Tailwind CSS v4 | Emi, Farid, Xavier |
| Lenguaje | TypeScript estricto | Todos |
| Offline | localStorage + PWA manifest | Emi |
| Deploy | Vercel (rama main) | Fidel |

---

## Arquitectura de carpetas

```
middleware.ts                          # RBAC: redirige por rol

constants/index.ts                     # GEMINI_MODEL, constantes globales
types/types.ts                         # tipos compartidos por todo el proyecto

hooks/
├── useItinerary.ts                    # localStorage del itinerario (offline)
├── useGeolocation.ts                  # navigator.geolocation
└── useAuth.ts                         # sesión Supabase en cliente

lib/
├── gemini.ts                          # cliente + ciclo while de function calling
├── equity.ts                          # score = (relevancia × proximidad) + β - saturación
├── cultural.ts                        # parámetros culturales por país de origen
├── businesses.ts                      # buscarNegocios() — mock → Supabase
├── itinerary.ts                       # agregarEvento(), leerItinerario()
├── maps.ts                            # Mapbox: rutas, geocoding
├── supabase.ts                        # cliente servidor (service role)
├── supabase-client.ts                 # cliente browser (solo auth)
└── tools/
    ├── index.ts                       # junta declarations + handlers
    ├── itinerary.ts                   # tools de itinerario para Gemini
    └── maps.ts                        # tools de mapas para Gemini

components/
├── ui/                                # Button, Card, Input, Badge
├── tourist/                           # BusinessCard, BusinessMap, Questionnaire, ItineraryView
├── business/                          # RequestForm
└── admin/                             # RequestCard, ReviewForm

app/
├── page.tsx                           # bienvenida — entrada por QR
├── auth/
│   ├── login/page.tsx
│   ├── register/page.tsx
│   └── callback/route.ts             # OAuth Google callback
├── (tourist)/
│   ├── onboarding/page.tsx
│   ├── map/page.tsx
│   ├── itinerary/page.tsx
│   └── profile/page.tsx
├── (business)/
│   ├── request/page.tsx
│   └── profile/page.tsx
├── (admin)/
│   ├── requests/page.tsx
│   └── requests/[id]/page.tsx
├── (superadmin)/
│   ├── tickets/page.tsx
│   └── monitoring/page.tsx
└── api/
    ├── chat/route.ts                  # Gemini function calling
    ├── recommend/route.ts             # equity + cultural → 4-6 tarjetas
    ├── itinerary/route.ts             # generar y editar itinerario
    ├── businesses/                    # CRUD negocios
    ├── requests/                      # solicitudes de negocio
    └── visits/route.ts               # registra visitas para equidad

supabase/migrations/
public/manifest.json                   # PWA
.env.local                             # nunca en git
```

---

## Variables de entorno

Todos necesitan un `.env.local` en la raíz con estas variables.
Pedirle los valores a Fidel o Alan antes de empezar.

```bash
# IA — solo servidor, nunca NEXT_PUBLIC_
GEMINI_API_KEY=

# Supabase — servidor
SUPABASE_URL=
SUPABASE_SERVICE_ROLE_KEY=

# Supabase — cliente (estas SÍ pueden ser NEXT_PUBLIC_)
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=

# Mapbox — cliente
NEXT_PUBLIC_MAPBOX_TOKEN=
```

**Regla:** si la variable no lleva `NEXT_PUBLIC_`, nunca llega al browser.

---

## API — resumen de endpoints

| Método | Endpoint | Rol | Qué hace |
|--------|----------|-----|----------|
| POST | `/api/chat` | Turista | Chat con Gemini + function calling |
| POST | `/api/recommend` | Turista | Genera 4-6 recomendaciones con equidad |
| POST | `/api/itinerary` | Turista | Genera itinerario automático |
| PATCH | `/api/itinerary/[id]` | Turista | Edita itinerario |
| GET | `/api/itinerary/[id]/route` | Turista | Ruta del día en GeoJSON |
| POST | `/api/businesses` | Público | Alta de solicitud de negocio |
| GET | `/api/businesses` | Turista | Lista negocios con filtros |
| POST | `/api/requests/[id]/review/claim` | Admin | Toma solicitud para revisión |
| POST | `/api/requests/[id]/review/approve` | Admin | Aprueba solicitud |
| POST | `/api/requests/[id]/review/reject` | Admin | Rechaza con feedback obligatorio |
| POST | `/api/visits` | Turista | Registra visita para algoritmo equidad |

Contratos completos → `API.md`

---

## Schema — tablas principales

| Tabla | Qué guarda |
|-------|-----------|
| `users_profile` | Perfil extendido del usuario (idioma, país, avatar) |
| `roles` / `user_roles` | RBAC — qué rol tiene cada usuario |
| `business_requests` | Solicitudes de alta de negocio (21 campos + estado) |
| `business_request_reviews` | Historial de acciones administrativas por solicitud |
| `business_profiles` | Negocios aprobados y activos |
| `tourist_questionnaires` | Cuestionarios de onboarding del turista |
| `recommendations` | Sesiones de recomendación con su contexto |
| `recommendation_items` | Negocios recomendados con score y razones |
| `itineraries` | Itinerarios generados (draft / saved / archived) |
| `itinerary_stops` | Paradas individuales por día del itinerario |
| `itinerary_daily_routes` | Caché de geometría de ruta por día |
| `visits` | Visitas registradas — alimenta el algoritmo de equidad |
| `daily_business_saturation` | Agregado diario de saturación por negocio |
| `technical_tickets` | Tickets técnicos para SuperAdmin |
| `audit_logs` | Auditoría de todas las transiciones de estado |

Schema completo → `SCHEMA.md`

---

## Ramas de trabajo

```
main                          ← solo Fidel mergea aquí
├── feat/equity-algorithm     ← Fidel
├── feat/gemini-chat          ← Fidel
├── feat/api-recommend        ← Fidel
├── feat/api-itinerary        ← Fidel
├── feat/tourist-onboarding   ← Emi
├── feat/tourist-map          ← Emi
├── feat/tourist-cards        ← Emi
├── feat/tourist-itinerary    ← Emi
├── feat/admin-requests       ← Farid
├── feat/business-form        ← Farid
├── feat/superadmin-panel     ← Farid
├── feat/database-schema      ← Alan
├── feat/auth-middleware      ← Alan
├── feat/api-businesses       ← Alan
├── feat/api-requests         ← Alan
├── feat/api-visits           ← Alan
├── feat/seed-data            ← Xavier
└── feat/ui-polish            ← Xavier
```

---

## Comandos esenciales

### Setup inicial
```bash
git clone <repo>
cd my-app
npm install
cp .env.example .env.local   # llenar con valores reales
npm run dev
```

### Flujo diario
```bash
git pull origin main          # siempre primero
git checkout feat/tu-rama     # cambia a tu rama
# ... codeas ...
npm run dev                   # prueba en browser
npx tsc --noEmit              # verifica tipos
npm run build                 # build de producción
git add archivo.ts            # nunca git add .
git commit -m "feat: descripción"
git push origin feat/tu-rama
```

### Comandos útiles
```bash
npm run lint                  # revisa reglas de ESLint
git log --oneline -10         # ver últimos 10 commits
git diff main                 # ver qué cambió vs main
git stash                     # guardar cambios sin commitear
git stash pop                 # recuperar cambios guardados
```

---

## Reglas TypeScript — no se rompen

```
✗ No usar any en ningún lado
✗ No usar NEXT_PUBLIC_ en claves de servidor (Gemini, Supabase service role)
✗ El frontend nunca importa lib/gemini.ts ni lib/supabase.ts directamente
✓ Tipar FunctionDeclaration[] explícitamente en lib/tools/
✓ Usar Content[] del SDK para el historial de Gemini
```

---

## URLs importantes

| Qué | URL |
|-----|-----|
| App producción | `https://mexgo.vercel.app` (pendiente) |
| Vercel dashboard | `https://vercel.com/los-mossitos` (pendiente) |
| Supabase dashboard | Pedirle a Alan |
| Google AI Studio (keys) | `https://aistudio.google.com` |
| Mapbox dashboard | Pedirle a Emi |

---

## Golden paths — flujos del demo

### Turista
```
1. Abre app por QR en celular
2. Login con Google (1 clic)
3. Cuestionario: "de EUA, 3 días, gastronomía"
4. Mapa con 4-6 negocios recomendados con sello Ola México
5. Chat: "¿dónde como tacos?" → Gemini recomienda → turista elige
6. Gemini pregunta día y hora → agrega al itinerario → confirma
7. Ve el itinerario guardado con la card del negocio
```

### EncargadoDelNegocio
```
1. Entra a la app → "Registra tu negocio"
2. Llena formulario de 21 campos (nombre, descripción, ubicación, etc.)
3. Envía solicitud → recibe correo "solicitud recibida"
4. Espera revisión del Admin
5. Si aprobado → recibe correo de acceso → entra a su perfil de negocio
6. Edita horario, foto de portada y redes sociales
```

### Admin
```
1. Login con cuenta admin
2. Ve lista de solicitudes en estado Pendiente
3. Abre solicitud → cambia a En revisión (se bloquea para otros admins)
4. Revisa los 21 campos del negocio
5a. Aprueba → negocio aparece en la plataforma → encargado recibe correo
5b. Rechaza → escribe feedback obligatorio → encargado puede corregir y reenviar
```

### SuperAdmin
```
1. Login con cuenta superadmin
2. Ve panel de monitoreo → saturación de negocios en tiempo real
3. Detecta incidencia → abre ticket técnico
4. Asigna severidad (low / medium / high / critical)
5. Resuelve → cierra ticket con notas de resolución
6. Revisa audit logs si hay disputa de acción administrativa
```

---

## Cambios

| Fecha | Quién | Qué |
|-------|-------|-----|
| 2026-04-06 | Fidel | v1.0 — referencia visual completa para todo el equipo |
