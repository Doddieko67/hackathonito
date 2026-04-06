# MexGo — SCHEMA
**Los Mossitos · Genius Arena 2026**

Modelo relacional para Supabase PostgreSQL.
Orientado a RBAC, flujo de solicitudes y registro de visitas para algoritmo de equidad.

---

## 1. Principios del modelo

- UUID como PK en tablas principales.
- Timestamps en UTC con timestamptz.
- Soft delete solo donde aplique a negocio publicado.
- Auditoría explícita para cambios de estado.
- Catálogos normalizados para consistencia y validación.

---

## 2. Enums recomendados

status_business_request:
- PENDIENTE
- EN_REVISION
- RECHAZADO
- APROBADO

role_code:
- TURISTA
- ENCARGADO_NEGOCIO
- ADMIN
- SUPERADMIN

training_campus:
- HUB_AZTECA
- MIDE

business_start_range:
- MENOS_1_ANO
- A1_A3
- A3_A5
- MAS_5

sat_status:
- FORMAL_REGISTRADO
- EN_PROCESO
- NO_PERO_INTERESA
- TAL_VEZ
- NO_NO_INTERESA

visit_event_type:
- VIEW
- CLICK_DIRECTIONS
- CHECKIN
- PURCHASE_CONFIRMED

ticket_status:
- OPEN
- IN_PROGRESS
- RESOLVED
- CLOSED

---

## 3. Tablas núcleo de usuarios y RBAC

### users_profile

Extiende auth.users de Supabase.

- id uuid pk references auth.users(id)
- full_name text not null
- avatar_url text null
- language_code text not null
- country_of_origin text not null
- email_verified boolean not null default false
- created_at timestamptz not null default now()
- updated_at timestamptz not null default now()

Índices:
- idx_users_profile_country (country_of_origin)
- idx_users_profile_language (language_code)

### roles

- id smallserial pk
- code role_code unique not null
- description text not null

### user_roles

- user_id uuid not null references auth.users(id)
- role_id smallint not null references roles(id)
- created_at timestamptz not null default now()
- created_by uuid null references auth.users(id)
- primary key (user_id, role_id)

Índices:
- idx_user_roles_role (role_id)

Regla:
- Un usuario puede tener múltiples roles, pero en runtime se valida contexto por endpoint.

---

## 4. Catálogos de negocio

### borough_catalog

- code text pk
- name text not null
- is_active boolean not null default true

Valores iniciales sugeridos:
- ALVARO_OBREGON
- AZCAPOTZALCO
- BENITO_JUAREZ
- COYOACAN
- CUAJIMALPA
- CUAUHTEMOC
- GUSTAVO_A_MADERO
- IZTAPALAPA
- MAGDALENA_CONTRERAS
- MIGUEL_HIDALGO
- MILPA_ALTA
- TLAHUAC
- TLALPAN
- VENUSTIANO_CARRANZA
- XOCHIMILCO
- OTRO

### business_operation_modes_catalog

- code text pk
- label text not null

Valores:
- LOCAL_FISICO
- VENTA_DOMICILIO
- BAZARES_EVENTOS_TIANGUIS
- VENTA_A_NEGOCIOS
- VENTA_AMBULANTE
- OTRO

---

## 5. Solicitudes de negocio y revisión

### business_requests

- id uuid pk default gen_random_uuid()
- owner_user_id uuid null references auth.users(id)
- owner_full_name text not null
- owner_age integer not null check (owner_age >= 18 and owner_age <= 100)
- owner_gender text not null
- owner_email citext not null
- owner_whatsapp text not null
- borough_code text not null references borough_catalog(code)
- neighborhood text not null
- google_maps_url text null
- training_campus_hint training_campus null
- employees_women_count integer not null default 0 check (employees_women_count >= 0)
- employees_men_count integer not null default 0 check (employees_men_count >= 0)
- business_name text not null
- business_description varchar(150) not null
- business_start_range business_start_range not null
- continuous_operation_time text not null
- operation_days_hours text not null
- operation_modes text[] not null
- operation_modes_other text null
- sat_status sat_status not null
- social_links text[] not null default '{}'
- adaptation_for_world_cup text not null
- support_usage text not null
- training_campus_preference training_campus not null
- additional_comments text null
- status status_business_request not null default 'PENDIENTE'
- current_lock_admin_user_id uuid null references auth.users(id)
- lock_acquired_at timestamptz null
- lock_expires_at timestamptz null
- submitted_at timestamptz not null default now()
- updated_at timestamptz not null default now()

Checks sugeridos:
- operation_modes cardinality >= 1
- si operation_modes contiene OTRO, operation_modes_other no null

Índices:
- idx_business_requests_status (status)
- idx_business_requests_lock (current_lock_admin_user_id)
- idx_business_requests_owner_email (owner_email)
- idx_business_requests_submitted_at (submitted_at desc)

### business_request_reviews

Historial de acciones administrativas.

- id uuid pk default gen_random_uuid()
- business_request_id uuid not null references business_requests(id)
- action text not null check (action in ('CLAIM','APPROVE','REJECT','RELEASE','RESUBMIT'))
- from_status status_business_request not null
- to_status status_business_request not null
- comment text null
- performed_by_user_id uuid not null references auth.users(id)
- created_at timestamptz not null default now()

Índices:
- idx_request_reviews_request (business_request_id, created_at desc)

### business_profiles

Se crea al aprobar solicitud.

- id uuid pk default gen_random_uuid()
- business_request_id uuid unique not null references business_requests(id)
- owner_user_id uuid not null references auth.users(id)
- business_name text not null
- business_description varchar(150) not null
- borough_code text not null references borough_catalog(code)
- neighborhood text not null
- google_maps_url text null
- operation_days_hours text not null
- social_links text[] not null default '{}'
- cover_image_url text null
- contact_phone text null
- is_active boolean not null default true
- created_at timestamptz not null default now()
- updated_at timestamptz not null default now()

Índices:
- idx_business_profiles_owner (owner_user_id)
- idx_business_profiles_active (is_active)

---

## 6. Cuestionario, recomendaciones e itinerario

### tourist_questionnaires

- id uuid pk default gen_random_uuid()
- tourist_user_id uuid not null references auth.users(id)
- payload jsonb not null
- created_at timestamptz not null default now()

Índice:
- idx_tourist_questionnaires_user (tourist_user_id, created_at desc)

### recommendations

- id uuid pk default gen_random_uuid()
- tourist_user_id uuid not null references auth.users(id)
- questionnaire_id uuid null references tourist_questionnaires(id)
- context_payload jsonb not null
- created_at timestamptz not null default now()

### recommendation_items

- id uuid pk default gen_random_uuid()
- recommendation_id uuid not null references recommendations(id)
- business_profile_id uuid not null references business_profiles(id)
- rank integer not null check (rank between 1 and 6)
- score numeric(8,5) not null
- reasons jsonb not null default '[]'::jsonb
- estimated_walk_minutes integer null
- created_at timestamptz not null default now()

Índices:
- idx_recommendation_items_recommendation (recommendation_id, rank)
- idx_recommendation_items_business (business_profile_id)

### itineraries

- id uuid pk default gen_random_uuid()
- tourist_user_id uuid not null references auth.users(id)
- recommendation_id uuid null references recommendations(id)
- status text not null check (status in ('draft','saved','archived')) default 'draft'
- version integer not null default 1
- itinerary_payload jsonb not null
- created_at timestamptz not null default now()
- updated_at timestamptz not null default now()

Índices:
- idx_itineraries_user (tourist_user_id, updated_at desc)

---

## 7. Visitas para algoritmo de equidad

### visits

Registro de visitas/acciones usadas para saturación y señales de conversión.

- id uuid pk default gen_random_uuid()
- tourist_user_id uuid not null references auth.users(id)
- business_profile_id uuid not null references business_profiles(id)
- recommendation_id uuid null references recommendations(id)
- itinerary_id uuid null references itineraries(id)
- source text not null check (source in ('itinerary','map','card'))
- event_type visit_event_type not null
- occurred_at timestamptz not null
- local_day date not null
- lat numeric(9,6) null
- lng numeric(9,6) null
- counted_for_equity boolean not null default true
- dedupe_key text not null
- created_at timestamptz not null default now()

Reglas:
- dedupe_key unique para evitar duplicados por reintento.
- local_day derivado de occurred_at en zona America/Mexico_City.

Índices:
- uq_visits_dedupe_key unique (dedupe_key)
- idx_visits_business_day (business_profile_id, local_day)
- idx_visits_business_occurred (business_profile_id, occurred_at desc)
- idx_visits_tourist_day (tourist_user_id, local_day)

### daily_business_saturation

Tabla agregada para consultas rápidas del algoritmo.

- business_profile_id uuid not null references business_profiles(id)
- day date not null
- visits_count integer not null default 0
- unique_tourists_count integer not null default 0
- last_referred_at timestamptz null
- saturation_score numeric(8,5) not null default 0
- updated_at timestamptz not null default now()
- primary key (business_profile_id, day)

Índices:
- idx_daily_saturation_day (day, saturation_score desc)

Mantenimiento:
- Trigger en visits actualiza agregados en inserción.

---

## 8. Tickets técnicos

### technical_tickets

- id uuid pk default gen_random_uuid()
- title text not null
- description text not null
- severity text not null check (severity in ('low','medium','high','critical'))
- status ticket_status not null default 'OPEN'
- opened_by_user_id uuid not null references auth.users(id)
- assigned_to_user_id uuid null references auth.users(id)
- resolution_notes text null
- created_at timestamptz not null default now()
- updated_at timestamptz not null default now()

Índices:
- idx_tickets_status (status)
- idx_tickets_severity (severity)

---

## 9. Auditoría

### audit_logs

- id uuid pk default gen_random_uuid()
- entity_type text not null
- entity_id uuid not null
- action text not null
- actor_user_id uuid null references auth.users(id)
- before_data jsonb null
- after_data jsonb null
- metadata jsonb not null default '{}'::jsonb
- created_at timestamptz not null default now()

Índices:
- idx_audit_entity (entity_type, entity_id, created_at desc)
- idx_audit_actor (actor_user_id, created_at desc)

---

## 10. Reglas de transición de estado (solicitudes)

Permitidas:
- Pendiente -> En revision (claim)
- En revision -> Aprobado (approve)
- En revision -> Rechazado (reject)
- En revision -> Pendiente (release)
- Rechazado -> Pendiente (resubmit)

No permitidas:
- Pendiente -> Aprobado directo
- Rechazado -> Aprobado directo
- Aprobado -> Pendiente

Control de concurrencia:
- Claim usa update condicional por status y lock nulo/no vigente.
- Si 0 rows affected, responder conflicto (resource_locked).

---

## 11. RLS (resumen operativo)

- users_profile: usuario ve y edita su fila.
- business_requests:
  - EncargadoDelNegocio ve/edita solo sus solicitudes permitidas por estado.
  - Admin ve todas.
  - SuperAdmin ve todas.
- business_profiles:
  - Público: lectura de campos publicados.
  - EncargadoDelNegocio: edición limitada de su negocio.
- visits:
  - Turista inserta solo para sí mismo.
  - Admin/SuperAdmin lectura agregada.
- audit_logs: solo Admin/SuperAdmin lectura.

Implementación recomendada:
- helper SQL has_role(auth.uid(), 'ADMIN').
- vistas seguras para panel admin.

---

## 12. SQL operativo mínimo (pseudobase)

1. Crear enums.
2. Crear catálogos base.
3. Crear tablas núcleo (users_profile, roles, user_roles).
4. Crear business_requests + reviews + profiles.
5. Crear cuestionario/recomendaciones/itinerarios.
6. Crear visits + daily_business_saturation + trigger de agregación.
7. Crear tickets y audit_logs.
8. Aplicar índices.
9. Aplicar políticas RLS.
10. Seed de roles y catálogos.

---

## 13. Pendientes menores antes de cierre final

- Confirmar si social_links será text[] o jsonb estructurado por red.
- Confirmar si owner_age se conservará completo o solo rango por privacidad.
- Definir retención de visits crudas y política de archivado.
- Definir umbral exacto de saturación_score para lib/equity.ts.

---

## Cambios

| Fecha | Quién | Qué |
|---|---|---|
| 2026-04-06 | Alan | v1.0 — modelo de datos, estados y visitas para algoritmo de equidad. |
