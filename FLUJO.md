# MexGo — Flujo de Trabajo
**Los Mossitos · Genius Arena 2026**

---

## Roles del equipo

| Quién | Rol | Tecnologías principales |
|-------|-----|------------------------|
| Fidel | Arquitecto, IA, algoritmo, deploy | Gemini API, `lib/equity.ts`, Next.js API Routes, Vercel |
| Alan | Auth, base de datos, backend | Supabase, PostgreSQL, middleware, RBAC |
| Emi | Frontend turista | Tailwind, Mapbox, PWA, localStorage |
| Farid | Frontend admin y negocio | Tailwind, Supabase Auth, Cloudinary |
| Xavier | QA, datos semilla, polish UI | Postman, Tailwind, datos JSON |

---

## Arquitectura de carpetas

```
middleware.ts                          # Alan — RBAC por rol

constants/index.ts                     # Fidel
types/types.ts                         # Xavier

hooks/
├── useItinerary.ts                    # Emi
├── useGeolocation.ts                  # Emi
└── useAuth.ts                         # Emi

lib/
├── gemini.ts                          # Fidel
├── equity.ts                          # Fidel
├── cultural.ts                        # Fidel
├── businesses.ts                      # Fidel (mock) → Alan (Supabase)
├── itinerary.ts                       # Fidel (mock) → Alan (Supabase)
├── maps.ts                            # Fidel
├── supabase.ts                        # Alan
├── supabase-client.ts                 # Alan
└── tools/
    ├── index.ts                       # Fidel
    ├── itinerary.ts                   # Fidel
    └── maps.ts                        # Fidel

components/
├── ui/                                # Xavier (polish)
├── tourist/                           # Emi
├── business/                          # Farid
└── admin/                             # Farid

app/
├── page.tsx                           # Emi
├── auth/                              # Alan
├── (tourist)/                         # Emi
├── (business)/                        # Farid
├── (admin)/                           # Farid
├── (superadmin)/                      # Farid
└── api/
    ├── chat/route.ts                  # Fidel
    ├── recommend/route.ts             # Fidel
    ├── itinerary/                     # Fidel
    ├── businesses/                    # Alan
    ├── requests/                      # Alan
    └── visits/route.ts               # Alan
```

---

## Ramas de trabajo

Nadie pushea directo a `main`. Cada feature tiene su rama.

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

## Flujo diario de trabajo

### Antes de empezar a codear

```bash
git pull origin main          # siempre primero
git checkout feat/tu-rama     # o git checkout -b feat/nueva-rama si no existe
```

### Antes de subir cambios — obligatorio en este orden

```bash
npm run dev                   # ¿funciona en el browser?
npx tsc --noEmit              # ¿TypeScript sin errores?
npm run build                 # ¿build de producción limpio?
```

Si cualquiera de los tres falla — **no subes**. Lo arreglas primero.

```bash
git add archivo1.ts archivo2.tsx    # agrega solo tus archivos, nunca git add .
git commit -m "feat: descripción clara de qué hiciste"
git push origin feat/tu-rama
```

### Flujo de merge a main

```
1. Tú subes tu rama (feat/lo-que-sea)
2. Xavier revisa en la preview URL de Vercel
   → prueba el flujo completo como usuario real
   → da observaciones o da el visto bueno
   → si pule UI: corre npm run build antes de avisar
3. Xavier le avisa a Fidel: "feat/tourist-map lista para merge"
4. Fidel corre npm run build local con la rama
5. Si pasa → merge a main → Vercel despliega automático
```

---

## Orden de importancia y dependencias

### 1. Alan — el cuello de botella crítico

Sin schema y sin datos reales, nadie puede conectar nada.
**Lo primero que hace Alan:** levantar Supabase, aplicar migraciones, publicar endpoints base.
Nadie empieza a codear contra un endpoint que Alan no haya documentado en `API.md`.

### 2. Xavier — desbloquea dos momentos distintos

**Momento 1 — Datos semilla** (día 1, mañana):
Sin datos de negocios verosímiles, el algoritmo de equidad no tiene qué mostrar.
Xavier genera el JSON de 50 negocios antes de que Fidel conecte `lib/equity.ts` a Supabase.

**Momento 2 — QA y polish** (día 2):
Revisa cada rama antes del merge. Es el filtro entre el código y `main`.

### 3. Fidel, Emi, Farid — trabajan en paralelo

Son independientes entre sí siempre que los contratos de API estén definidos.
Fidel trabaja con mocks hasta que Alan tenga los endpoints listos.
Emi y Farid trabajan con datos hardcodeados hasta que Fidel tenga los API routes.

---

## Archivos por persona

### Fidel

| Archivo | Qué hace |
|---------|----------|
| `constants/index.ts` | Modelo de Gemini y constantes globales — si cambia el modelo, solo aquí |
| `lib/gemini.ts` | Cliente Gemini + ciclo `while` de function calling |
| `lib/equity.ts` | Algoritmo de equidad: `score = (relevancia × proximidad) + β - saturación` |
| `lib/cultural.ts` | Parámetros culturales por país: precio accesible, tolerancia de caminata, preferencias |
| `lib/businesses.ts` | `buscarNegocios()` — mock hoy, Supabase cuando Alan esté listo |
| `lib/itinerary.ts` | `agregarEvento()`, `leerItinerario()` — mock hoy, Supabase después |
| `lib/tools/index.ts` | Junta declarations y handlers de todos los dominios para Gemini |
| `lib/tools/itinerary.ts` | Tool declarations + handlers: buscar_negocios, agregar_evento, leer_itinerario |
| `lib/tools/maps.ts` | Tool declarations + handlers: buscar_ruta, geocodificar |
| `app/api/chat/route.ts` | BFF del chat — recibe mensaje, llama Gemini, devuelve respuesta |
| `app/api/recommend/route.ts` | BFF de recomendaciones — aplica cultural + equity → 4-6 tarjetas |
| `app/api/itinerary/route.ts` | BFF de itinerario — genera y edita itinerario con Gemini |
| `next.config.ts` | Configuración de Next.js y PWA |
| `.env.local` / `.env.example` | Variables de entorno del servidor — nunca en git |

### Alan

| Archivo | Qué hace |
|---------|----------|
| `middleware.ts` | RBAC — lee el JWT de Supabase y redirige al grupo correcto por rol |
| `lib/supabase.ts` | Cliente Supabase con service role — solo en servidor |
| `lib/supabase-client.ts` | Cliente Supabase para el browser — solo para auth |
| `app/auth/callback/route.ts` | Callback de OAuth Google — Supabase lo llama tras autenticación |
| `app/api/businesses/` | GET negocios con filtros, GET detalle, PATCH edición por encargado |
| `app/api/requests/` | POST nueva solicitud, GET lista admin, claim/approve/reject/release |
| `app/api/visits/route.ts` | POST visita — alimenta `daily_business_saturation` para el algoritmo |
| `supabase/migrations/` | Migraciones SQL — schema completo, enums, índices, RLS, triggers |

### Emi

| Archivo | Qué hace |
|---------|----------|
| `hooks/useItinerary.ts` | Sincroniza itinerario con localStorage — funciona offline |
| `hooks/useGeolocation.ts` | Captura lat/lng del browser con `navigator.geolocation` |
| `hooks/useAuth.ts` | Lee sesión activa de Supabase en el cliente |
| `components/tourist/BusinessCard.tsx` | Tarjeta deslizable con foto, nombre, distancia y sello Ola México |
| `components/tourist/BusinessMap.tsx` | Mapa con pins de negocios recomendados |
| `components/tourist/Questionnaire.tsx` | Flujo conversacional de onboarding del turista |
| `components/tourist/ItineraryView.tsx` | Vista de eventos del itinerario por día |
| `app/(tourist)/onboarding/page.tsx` | Cuestionario + pregunta de boleto de partido |
| `app/(tourist)/map/page.tsx` | Mapa + tarjetas deslizables con recomendaciones |
| `app/(tourist)/itinerary/page.tsx` | Itinerario completo + descarga PDF |
| `app/(tourist)/profile/page.tsx` | Editar idioma, país y preferencias del turista |
| `app/page.tsx` | Pantalla de bienvenida — entrada por QR, sin login requerido |

### Farid

| Archivo | Qué hace |
|---------|----------|
| `app/auth/login/page.tsx` | Login con Google OAuth + email/password |
| `app/auth/register/page.tsx` | Registro para EncargadoDelNegocio |
| `components/business/RequestForm.tsx` | Formulario de alta de negocio (21 campos) |
| `components/admin/RequestCard.tsx` | Card de solicitud pendiente con estado y última acción |
| `components/admin/ReviewForm.tsx` | Formulario para aprobar o rechazar con comentario obligatorio |
| `app/(business)/request/page.tsx` | Formulario completo de solicitud de negocio |
| `app/(business)/profile/page.tsx` | Perfil del negocio aprobado — edición limitada |
| `app/(admin)/requests/page.tsx` | Lista de solicitudes filtrable por estado |
| `app/(admin)/requests/[id]/page.tsx` | Vista de revisión — claim, aprobar o rechazar |
| `app/(superadmin)/tickets/page.tsx` | Gestión de tickets técnicos |
| `app/(superadmin)/monitoring/page.tsx` | Saturación en tiempo real y audit logs |

### Xavier

| Archivo | Qué hace |
|---------|----------|
| `types/types.ts` | Tipos TypeScript compartidos por todo el proyecto |
| `components/ui/` | Componentes compartidos: Button, Card, Input, Badge |
| `seed/negocios.json` | 50 negocios verosímiles con coords, fotos y sello Ola México |

---

## Cambios

| Fecha | Quién | Qué |
|-------|-------|-----|
| 2026-04-06 | Fidel | v1.0 — flujo de trabajo, ramas, dependencias y archivos por persona |
