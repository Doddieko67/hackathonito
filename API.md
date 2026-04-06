# MexGo — API
**Los Mossitos · Genius Arena 2026**

Contratos backend. Base para implementación en Next.js API Routes + Supabase.

---

## 1. Convenciones generales

- Base path: /api
- Formato: JSON (UTF-8)
- Auth: Bearer JWT de Supabase Auth
- Roles: Turista, EncargadoDelNegocio, Admin, SuperAdmin
- Timezone de negocio: America/Mexico_City
- Idempotencia recomendada en operaciones sensibles con header opcional:
  - Idempotency-Key

### Respuesta exitosa estándar

{
  "ok": true,
  "data": {},
  "meta": {
    "requestId": "uuid",
    "timestamp": "2026-04-06T12:00:00Z"
  }
}

### Respuesta de error estándar

{
  "ok": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Descripcion clara",
    "details": []
  },
  "meta": {
    "requestId": "uuid",
    "timestamp": "2026-04-06T12:00:00Z"
  }
}

### Códigos de error

- AUTH_REQUIRED
- FORBIDDEN
- VALIDATION_ERROR
- NOT_FOUND
- CONFLICT
- STATE_TRANSITION_NOT_ALLOWED
- RESOURCE_LOCKED
- RATE_LIMITED
- INTERNAL_ERROR

---

## 2. Autenticación y perfil Turista

### POST /api/auth/register

Registro Turista por email/password.

Auth: Pública

Body:
{
  "email": "turista@correo.com",
  "password": "***",
  "fullName": "Nombre",
  "language": "es-MX",
  "countryOfOrigin": "MX"
}

Reglas:
- language y countryOfOrigin obligatorios.
- Dispara email de verificación.

Response 201:
{
  "ok": true,
  "data": {
    "userId": "uuid",
    "emailVerificationSent": true
  }
}

### POST /api/auth/oauth/start

Inicia OAuth con Google o Apple.

Auth: Pública

Body:
{
  "provider": "google",
  "redirectTo": "https://app/callback"
}

Response 200:
{
  "ok": true,
  "data": {
    "authUrl": "https://..."
  }
}

### POST /api/auth/oauth/complete

Completa OAuth y exige onboarding mínimo si faltan datos.

Auth: Pública

Body:
{
  "provider": "google",
  "code": "...",
  "language": "es-MX",
  "countryOfOrigin": "US"
}

Response 200:
{
  "ok": true,
  "data": {
    "session": {
      "accessToken": "...",
      "refreshToken": "..."
    },
    "needsEmailVerification": false
  }
}

### PATCH /api/tourist/profile

Actualiza perfil Turista.

Auth: Turista

Body (parcial):
{
  "fullName": "Nuevo Nombre",
  "language": "en-US",
  "countryOfOrigin": "CA",
  "avatarUrl": "https://...",
  "password": "***"
}

Response 200: perfil actualizado.

### GET /api/tourist/profile

Auth: Turista

Response 200:
{
  "ok": true,
  "data": {
    "userId": "uuid",
    "fullName": "Nombre",
    "email": "turista@correo.com",
    "language": "es-MX",
    "countryOfOrigin": "MX",
    "avatarUrl": null,
    "emailVerified": true
  }
}

---

## 3. Cuestionario, recomendaciones e itinerarios

### POST /api/recomendar

Genera recomendaciones con perfil cultural + score de equidad.

Auth: Turista

Body:
{
  "questionnaire": {
    "travelStyle": "cultural",
    "groupType": "family",
    "budgetBand": "medio",
    "walkingToleranceMinutes": 25,
    "days": 3,
    "foodPreferences": ["street-food"]
  },
  "location": {
    "lat": 19.43,
    "lng": -99.13
  },
  "matchContext": {
    "hasTicket": true,
    "matchDateTime": "2026-06-14T19:00:00-06:00",
    "stadium": "Azteca"
  }
}

Proceso backend:
- Toma language y countryOfOrigin del perfil.
- Aplica lib/cultural.ts.
- Calcula score con lib/equity.ts usando visitas del dia y saturación.
- Devuelve 4 a 6 negocios + razones de recomendación.

Response 200:
{
  "ok": true,
  "data": {
    "recommendationId": "uuid",
    "items": [
      {
        "businessId": "uuid",
        "score": 0.86,
        "reasons": ["cercania", "baja saturacion"],
        "estimatedWalkMinutes": 12
      }
    ]
  }
}

### POST /api/itinerario

Genera itinerario automático desde recomendaciones.

Auth: Turista

Body:
{
  "recommendationId": "uuid",
  "days": [
    {
      "date": "2026-06-14",
      "timeBlocks": [
        {"start": "09:00", "end": "12:00", "status": "available"}
      ]
    }
  ],
  "regenerate": false
}

Response 201:
{
  "ok": true,
  "data": {
    "itineraryId": "uuid",
    "version": 1,
    "status": "draft",
    "days": []
  }
}

### PATCH /api/itinerario/{itineraryId}

Edición manual del itinerario (agregar/quitar/reordenar).

Auth: Turista dueño

Body:
{
  "operations": [
    {
      "op": "add_item",
      "date": "2026-06-14",
      "start": "13:00",
      "end": "14:30",
      "businessId": "uuid"
    },
    {
      "op": "remove_item",
      "itemId": "uuid"
    }
  ]
}

Response 200: itinerario actualizado.

### POST /api/itinerario/{itineraryId}/save

Marca itinerario como guardado por usuario.

Auth: Turista dueño

Response 200:
{
  "ok": true,
  "data": {
    "itineraryId": "uuid",
    "status": "saved"
  }
}

### POST /api/itinerario/{itineraryId}/pdf

Genera y devuelve URL temporal del PDF.

Auth: Turista dueño

Response 200:
{
  "ok": true,
  "data": {
    "downloadUrl": "https://...",
    "expiresAt": "2026-04-06T20:00:00Z"
  }
}

### GET /api/itinerario/{itineraryId}/route

Obtiene la ruta del mapa para un dia del itinerario en formato GeoJSON.

Auth: Turista dueño

Query:
- date: YYYY-MM-DD (obligatorio)
- profile: walking | driving (opcional, default walking)
- refresh: true | false (opcional, default false)

Reglas:
- Usa coordenadas de los lugares del itinerario.
- Si existe ruta cacheada y refresh=false, devuelve esa.
- Si refresh=true o la ruta esta stale, recalcula con provider de rutas.

Response 200:
{
  "ok": true,
  "data": {
    "itineraryId": "uuid",
    "date": "2026-06-14",
    "provider": "mapbox-directions",
    "profile": "walking",
    "distanceMeters": 4820,
    "durationSeconds": 4210,
    "waypoints": [
      {"order": 1, "label": "Tacos Dona Ana", "lat": 19.345123, "lng": -99.123456},
      {"order": 2, "label": "Cafe Barrio Azteca", "lat": 19.347000, "lng": -99.129000}
    ],
    "geometry": {
      "type": "LineString",
      "coordinates": [[-99.123456, 19.345123], [-99.129000, 19.347000]]
    }
  }
}

Errores:
- 409 CONFLICT si hay menos de 2 puntos con coordenadas validas.
- 422 VALIDATION_ERROR si la fecha no pertenece al itinerario.

### POST /api/itinerario/{itineraryId}/route/rebuild

Fuerza recalculo de ruta para uno o varios dias del itinerario.

Auth: Turista dueño

Body:
{
  "dates": ["2026-06-14"],
  "profile": "walking"
}

Response 200:
{
  "ok": true,
  "data": {
    "rebuilt": 1
  }
}

---

## 4. Solicitud de registro de negocio (EncargadoDelNegocio)

### POST /api/business-requests

Crea solicitud de negocio.

Auth: Pública o usuario autenticado sin rol admin

Body (21 campos):
{
  "ownerFullName": "...",
  "ownerAge": 35,
  "ownerGender": "femenino",
  "ownerEmail": "...",
  "ownerWhatsapp": "...",
  "borough": "Coyoacan",
  "neighborhood": "Santa Ursula",
  "googleMapsUrl": "https://maps.google.com/...",
  "latitude": null,
  "longitude": null,
  "trainingCampusHint": "HUB_AZTECA",
  "employeesWomenCount": 2,
  "employeesMenCount": 1,
  "businessName": "...",
  "businessDescription": "max 150 chars",
  "businessStartRange": "1_3_YEARS",
  "continuousOperationTime": "2 anos",
  "operationDaysHours": "Lun-Sab 10:00-20:00",
  "operationModes": ["LOCAL_FISICO", "BAZARES"],
  "operationModesOther": null,
  "satStatus": "EN_PROCESO",
  "socialLinks": ["https://instagram.com/..."],
  "adaptationForWorldCup": "...",
  "supportUsage": "...",
  "trainingCampusPreference": "MIDE",
  "additionalComments": "..."
}

Reglas:
- Estado inicial siempre Pendiente.
- businessDescription max 150.
- operationModes mínimo 1.
- latitude/longitude opcionales en alta; si faltan, se intentan derivar por geocoding en revision/aprobacion.
- Notificación por correo: solicitud recibida.

Response 201:
{
  "ok": true,
  "data": {
    "businessRequestId": "uuid",
    "status": "PENDIENTE"
  }
}

### PATCH /api/business-requests/{id}/resubmit

Reenvío tras correcciones.

Auth: EncargadoDelNegocio dueño

Body:
{
  "patch": {
    "businessDescription": "...",
    "socialLinks": ["..."]
  }
}

Regla:
- Solo permitido si estado actual Rechazado.
- Nuevo estado Pendiente.

Response 200: solicitud reenviada.

### GET /api/business-requests/me

Lista solicitudes del EncargadoDelNegocio autenticado.

Auth: EncargadoDelNegocio

Response 200:
{
  "ok": true,
  "data": {
    "items": [
      {
        "businessRequestId": "uuid",
        "status": "EN_REVISION",
        "lastFeedback": "Adjunta evidencia SAT",
        "updatedAt": "..."
      }
    ]
  }
}

---

## 5. Revisión administrativa de solicitudes

### GET /api/admin/business-requests

Lista por estado.

Auth: Admin, SuperAdmin

Query:
- status: PENDIENTE | EN_REVISION | RECHAZADO | APROBADO
- page, pageSize

### POST /api/admin/business-requests/{id}/claim

Toma de solicitud para revisión.

Auth: Admin

Reglas:
- Solo si estado actual Pendiente.
- Cambia a En revision.
- Bloquea para otros admins con lock temporal renovable.

Response 200:
{
  "ok": true,
  "data": {
    "businessRequestId": "uuid",
    "status": "EN_REVISION",
    "claimedBy": "admin-user-id"
  }
}

Errores:
- 409 RESOURCE_LOCKED si ya fue tomada.
- 409 STATE_TRANSITION_NOT_ALLOWED si no está en Pendiente.

### POST /api/admin/business-requests/{id}/approve

Aprueba solicitud.

Auth: Admin

Body:
{
  "comment": "Aprobado"
}

Reglas:
- Solo desde En revision por el mismo admin que la tomó.
- Crea business_profile activo.
- Vincula cuenta del EncargadoDelNegocio.
- Envía correo de aprobación y acceso.

### POST /api/admin/business-requests/{id}/reject

Rechaza solicitud con feedback.

Auth: Admin

Body:
{
  "feedback": "Falta completar campo SAT",
  "allowResubmit": true
}

Reglas:
- feedback obligatorio.
- Cambia estado a Rechazado.
- Envía correo con observaciones.

### POST /api/admin/business-requests/{id}/release

Libera revisión si no se concluye.

Auth: Admin dueño del lock o SuperAdmin

Reglas:
- Cambia En revision a Pendiente.
- Registra motivo.

---

## 6. Perfil de negocio (tras aprobación)

### GET /api/business/profile

Auth: EncargadoDelNegocio aprobado

### PATCH /api/business/profile

Auth: EncargadoDelNegocio aprobado

Campos editables limitados:
- businessDescription
- operationDaysHours
- socialLinks
- coverImageUrl
- contactPhone

Campos visibles de ubicacion:
- latitude
- longitude
- googleMapsUrl

Campos no editables por este rol:
- satStatus (solo flujo administrativo)
- ownerIdentityData
- estado de solicitud

---

## 7. Registro de visitas para algoritmo de equidad

Visita = interacción válida de Turista con negocio recomendando por sistema.
Se usa para saturación diaria y desempate de equidad.

### POST /api/visits

Auth: Turista

Body:
{
  "businessId": "uuid",
  "itineraryId": "uuid",
  "source": "itinerary",
  "eventType": "CHECKIN",
  "occurredAt": "2026-06-14T15:20:00-06:00",
  "lat": 19.43,
  "lng": -99.13
}

Reglas antifraude mínimas:
- Solo si businessId estaba recomendado al usuario (ventana configurable).
- Límite por turista/negocio/día para evitar spam.
- Distancia máxima opcional para CHECKIN físico.

Response 201:
{
  "ok": true,
  "data": {
    "visitId": "uuid",
    "countedForEquity": true
  }
}

### GET /api/admin/visits/summary

Auth: Admin, SuperAdmin

Query:
- date
- borough

Response 200:
{
  "ok": true,
  "data": {
    "businesses": [
      {
        "businessId": "uuid",
        "visitsToday": 23,
        "saturationLevel": 0.74,
        "lastReferredAt": "..."
      }
    ]
  }
}

---

## 8. Tickets técnicos (SuperAdmin)

### POST /api/tickets

Auth: Admin, SuperAdmin

Body:
{
  "title": "Error 500 en /api/itinerario",
  "description": "...",
  "severity": "high"
}

### GET /api/superadmin/tickets

Auth: SuperAdmin

### PATCH /api/superadmin/tickets/{id}

Auth: SuperAdmin

Body:
{
  "status": "IN_PROGRESS",
  "resolutionNotes": "..."
}

---

## 9. Matriz de permisos resumida

| Endpoint | Turista | EncargadoDelNegocio | Admin | SuperAdmin |
|---|---:|---:|---:|---:|
| POST /api/recomendar | Si | No | No | No |
| POST /api/itinerario | Si | No | No | No |
| PATCH /api/itinerario/{id} | Si (dueno) | No | No | No |
| GET /api/itinerario/{id}/route | Si (dueno) | No | No | No |
| POST /api/itinerario/{id}/route/rebuild | Si (dueno) | No | No | No |
| POST /api/business-requests | Si/No auth | Si | No | No |
| PATCH /api/business-requests/{id}/resubmit | No | Si (dueno) | No | No |
| POST /api/admin/business-requests/{id}/claim | No | No | Si | No |
| POST /api/admin/business-requests/{id}/approve | No | No | Si | No |
| POST /api/admin/business-requests/{id}/reject | No | No | Si | No |
| POST /api/visits | Si | No | No | No |
| GET /api/admin/visits/summary | No | No | Si | Si |
| GET /api/superadmin/tickets | No | No | No | Si |

---

## 10. Pendientes menores antes de cerrar final

- Definir provider exacto para envío de correo transaccional.
- Ajustar rate limits por endpoint en producción.
- Cerrar formato exacto del PDF (plantilla visual).
- Confirmar fallback de rutas cuando Directions API no responda.
- Versionar contrato definitivo en OpenAPI 3.1.

---

## Cambios

| Fecha | Quién | Qué |
|---|---|---|
| 2026-04-06 | Alan | v1.0 — contratos casi finales por rol, estados y visitas para equidad. |
| 2026-04-06 | Alan | v1.1 — rutas de itinerario en mapa, rebuild de ruta y coordenadas de lugares. |
