# MexGo — Tablas del Schema
**Los Mossitos · Genius Arena 2026**

Visualización en formato de tabla de todas las tablas de `SCHEMA.md`.
Referencia rápida para Alan, Fidel y Xavier.

---

## Enums

| Enum | Valores |
|------|---------|
| `status_business_request` | PENDIENTE, EN_REVISION, RECHAZADO, APROBADO |
| `role_code` | TURISTA, ENCARGADO_NEGOCIO, ADMIN, SUPERADMIN |
| `training_campus` | HUB_AZTECA, MIDE |
| `business_start_range` | MENOS_1_ANO, A1_A3, A3_A5, MAS_5 |
| `sat_status` | FORMAL_REGISTRADO, EN_PROCESO, NO_PERO_INTERESA, TAL_VEZ, NO_NO_INTERESA |
| `visit_event_type` | VIEW, CLICK_DIRECTIONS, CHECKIN, PURCHASE_CONFIRMED |
| `ticket_status` | OPEN, IN_PROGRESS, RESOLVED, CLOSED |

---

## Usuarios y RBAC

### users_profile
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | references auth.users(id) |
| full_name | text | not null |
| avatar_url | text | null |
| language_code | text | not null |
| country_of_origin | text | not null |
| email_verified | boolean | default false |
| created_at | timestamptz | default now() |
| updated_at | timestamptz | default now() |

### roles
| Columna | Tipo | Notas |
|---------|------|-------|
| id | smallserial PK | |
| code | role_code | unique not null |
| description | text | not null |

### user_roles
| Columna | Tipo | Notas |
|---------|------|-------|
| user_id | uuid | FK → auth.users — PK compuesta |
| role_id | smallint | FK → roles — PK compuesta |
| created_at | timestamptz | default now() |
| created_by | uuid | FK → auth.users, null |

---

## Catálogos

### borough_catalog
| Columna | Tipo | Notas |
|---------|------|-------|
| code | text PK | ej. COYOACAN, CUAUHTEMOC |
| name | text | not null |
| is_active | boolean | default true |

### business_operation_modes_catalog
| Columna | Tipo | Notas |
|---------|------|-------|
| code | text PK | ej. LOCAL_FISICO, VENTA_DOMICILIO |
| label | text | not null |

---

## Solicitudes de negocio

### business_requests
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| owner_user_id | uuid | FK → auth.users, null |
| owner_full_name | text | not null |
| owner_age | integer | check 18–100 |
| owner_gender | text | not null |
| owner_email | citext | not null |
| owner_whatsapp | text | not null |
| borough_code | text | FK → borough_catalog |
| neighborhood | text | not null |
| google_maps_url | text | null |
| latitude | numeric(9,6) | null |
| longitude | numeric(9,6) | null |
| geocode_source | text | null |
| geocode_confidence | numeric(5,2) | null |
| training_campus_hint | training_campus | null |
| employees_women_count | integer | default 0 |
| employees_men_count | integer | default 0 |
| business_name | text | not null |
| business_description | varchar(150) | not null |
| business_start_range | business_start_range | not null |
| continuous_operation_time | text | not null |
| operation_days_hours | text | not null |
| operation_modes | text[] | mínimo 1 |
| operation_modes_other | text | null |
| sat_status | sat_status | not null |
| social_links | text[] | default '{}' |
| adaptation_for_world_cup | text | not null |
| support_usage | text | not null |
| training_campus_preference | training_campus | not null |
| additional_comments | text | null |
| status | status_business_request | default PENDIENTE |
| current_lock_admin_user_id | uuid | FK → auth.users, null |
| lock_acquired_at | timestamptz | null |
| lock_expires_at | timestamptz | null |
| submitted_at | timestamptz | default now() |
| updated_at | timestamptz | default now() |

### business_request_reviews
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| business_request_id | uuid | FK → business_requests |
| action | text | CLAIM, APPROVE, REJECT, RELEASE, RESUBMIT |
| from_status | status_business_request | not null |
| to_status | status_business_request | not null |
| comment | text | null |
| performed_by_user_id | uuid | FK → auth.users |
| created_at | timestamptz | default now() |

### business_profiles
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| business_request_id | uuid | unique FK → business_requests |
| owner_user_id | uuid | FK → auth.users |
| business_name | text | not null |
| business_description | varchar(150) | not null |
| borough_code | text | FK → borough_catalog |
| neighborhood | text | not null |
| google_maps_url | text | null |
| latitude | numeric(9,6) | not null |
| longitude | numeric(9,6) | not null |
| geocoded_at | timestamptz | null |
| location_source | text | default 'request_or_geocode' |
| operation_days_hours | text | not null |
| social_links | text[] | default '{}' |
| cover_image_url | text | null |
| contact_phone | text | null |
| is_active | boolean | default true |
| created_at | timestamptz | default now() |
| updated_at | timestamptz | default now() |

---

## Cuestionario, recomendaciones e itinerario

### tourist_questionnaires
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| tourist_user_id | uuid | FK → auth.users |
| payload | jsonb | respuestas del cuestionario |
| created_at | timestamptz | default now() |

### recommendations
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| tourist_user_id | uuid | FK → auth.users |
| questionnaire_id | uuid | FK → tourist_questionnaires, null |
| context_payload | jsonb | contexto cultural + perfil usado |
| created_at | timestamptz | default now() |

### recommendation_items
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| recommendation_id | uuid | FK → recommendations |
| business_profile_id | uuid | FK → business_profiles |
| rank | integer | check 1–6 |
| score | numeric(8,5) | resultado del algoritmo equity |
| reasons | jsonb | ej. ["cercania","baja saturacion"] |
| estimated_walk_minutes | integer | null |
| created_at | timestamptz | default now() |

### itineraries
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| tourist_user_id | uuid | FK → auth.users |
| recommendation_id | uuid | FK → recommendations, null |
| status | text | draft / saved / archived |
| version | integer | default 1 |
| itinerary_payload | jsonb | estructura completa del itinerario |
| created_at | timestamptz | default now() |
| updated_at | timestamptz | default now() |

### itinerary_stops
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| itinerary_id | uuid | FK → itineraries |
| route_date | date | not null |
| stop_order | integer | check >= 1 |
| stop_type | text | BUSINESS, POI, MATCH, CUSTOM |
| business_profile_id | uuid | FK → business_profiles, null |
| label | text | not null |
| start_time | time | null |
| end_time | time | null |
| latitude | numeric(9,6) | not null |
| longitude | numeric(9,6) | not null |
| created_at | timestamptz | default now() |

### itinerary_daily_routes
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| itinerary_id | uuid | FK → itineraries |
| route_date | date | not null |
| provider | text | default 'mapbox-directions' |
| profile | text | walking / driving |
| waypoints | jsonb | default '[]' |
| route_geometry | jsonb | GeoJSON LineString |
| distance_meters | integer | default 0 |
| duration_seconds | integer | default 0 |
| is_stale | boolean | default false |
| generated_at | timestamptz | default now() |
| updated_at | timestamptz | default now() |

---

## Visitas y equidad

### visits
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| tourist_user_id | uuid | FK → auth.users |
| business_profile_id | uuid | FK → business_profiles |
| recommendation_id | uuid | FK → recommendations, null |
| itinerary_id | uuid | FK → itineraries, null |
| source | text | itinerary / map / card |
| event_type | visit_event_type | VIEW, CHECKIN, etc. |
| occurred_at | timestamptz | not null |
| local_day | date | derivado de occurred_at en Mexico_City |
| lat | numeric(9,6) | null |
| lng | numeric(9,6) | null |
| counted_for_equity | boolean | default true |
| dedupe_key | text | unique — evita duplicados |
| created_at | timestamptz | default now() |

### daily_business_saturation
| Columna | Tipo | Notas |
|---------|------|-------|
| business_profile_id | uuid | FK → business_profiles — PK compuesta |
| day | date | PK compuesta |
| visits_count | integer | default 0 |
| unique_tourists_count | integer | default 0 |
| last_referred_at | timestamptz | null |
| saturation_score | numeric(8,5) | default 0 — usado por lib/equity.ts |
| updated_at | timestamptz | default now() |

> Trigger en `visits` actualiza esta tabla en cada inserción.

---

## Tickets y auditoría

### technical_tickets
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| title | text | not null |
| description | text | not null |
| severity | text | low / medium / high / critical |
| status | ticket_status | default OPEN |
| opened_by_user_id | uuid | FK → auth.users |
| assigned_to_user_id | uuid | FK → auth.users, null |
| resolution_notes | text | null |
| created_at | timestamptz | default now() |
| updated_at | timestamptz | default now() |

### audit_logs
| Columna | Tipo | Notas |
|---------|------|-------|
| id | uuid PK | gen_random_uuid() |
| entity_type | text | ej. 'business_request' |
| entity_id | uuid | id de la entidad afectada |
| action | text | ej. 'APPROVE', 'REJECT' |
| actor_user_id | uuid | FK → auth.users, null |
| before_data | jsonb | estado anterior, null |
| after_data | jsonb | estado nuevo, null |
| metadata | jsonb | default '{}' |
| created_at | timestamptz | default now() |

---

## Transiciones de estado — solicitudes

| Desde | Acción | Hacia | Quién |
|-------|--------|-------|-------|
| PENDIENTE | claim | EN_REVISION | Admin |
| EN_REVISION | approve | APROBADO | Admin (mismo que hizo claim) |
| EN_REVISION | reject | RECHAZADO | Admin (mismo que hizo claim) |
| EN_REVISION | release | PENDIENTE | Admin o SuperAdmin |
| RECHAZADO | resubmit | PENDIENTE | EncargadoDelNegocio |
| PENDIENTE | — | APROBADO | ✗ No permitido |
| RECHAZADO | — | APROBADO | ✗ No permitido |
| APROBADO | — | PENDIENTE | ✗ No permitido |

---

## RLS por tabla

| Tabla | Turista | EncargadoDelNegocio | Admin | SuperAdmin |
|-------|---------|---------------------|-------|------------|
| users_profile | solo su fila | solo su fila | solo su fila | solo su fila |
| business_requests | no | solo las suyas | todas | todas |
| business_profiles | lectura pública | edición limitada de la suya | todas | todas |
| itineraries / stops | CRUD solo los suyos | no | lectura soporte | lectura soporte |
| visits | inserta solo para sí | no | lectura agregada | lectura agregada |
| audit_logs | no | no | lectura | lectura |
| technical_tickets | no | no | crear | CRUD |

---

## Cambios

| Fecha | Quién | Qué |
|-------|-------|-----|
| 2026-04-06 | Fidel | v1.0 — visualización en tablas de todo SCHEMA.md |
