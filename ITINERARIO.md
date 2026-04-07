# MexGo — Itinerario & Function Calling
**Los Mossitos · Genius Arena 2026**

---

## Orden de creación — guía para nuevas funcionalidades

Seguir este orden evita importar cosas que no existen aún.

**1. `types/types.ts`**
Define los tipos del dominio primero. Todo lo demás los importa.

**2. `constants/index.ts`**
Constantes compartidas: modelo de Gemini, radios de búsqueda, límites, etc.
Si cambia el modelo, un solo lugar.

**3. `.env.local`**
Claves secretas del servicio nuevo. Nunca `NEXT_PUBLIC_`, nunca en git.

**4. `lib/[categoria].ts`**
Una responsabilidad por archivo. Implementación real de los datos.
Hoy retorna mocks — cuando Alan tenga Supabase, solo cambia este archivo.
```
lib/negocios.ts    → datos de negocios
lib/itinerario.ts  → agregar y leer eventos
```

**5. `lib/tools/[categoria].ts`** *(cuando haya múltiples dominios)*
Declarations (JSON Schema para Gemini) + handlers (qué función llamar).
Registrar en `lib/tools/index.ts`.

**6. `lib/gemini.ts`**
Importa declarations y handlers. Contiene el ciclo `while` de function calling.
No tiene lógica de dominio — solo orquesta.

**7. `app/api/[funcionalidad]/route.ts`**
Recibe el request del browser, llama `lib/gemini.ts`, devuelve la respuesta.
Sin lógica propia.

**8. `hooks/use[Funcionalidad].ts`** *(si necesita persistencia en el browser)*
Sincroniza estado de React con `localStorage`. Útil para offline y persistencia
entre recargas. Solo se usa en componentes con `'use client'`.

**9. Frontend (`app/page.tsx` o componente)**
Solo hace `fetch` al API route. Nunca llama a Gemini directo.
Renderiza lo que recibe — texto como burbuja, eventos como cards.

---

## Flujo de function calling

```
Browser → POST /api/chat { mensaje, historial }
              ↓
         lib/gemini.ts
              ↓
         Gemini decide qué función llamar
              ↓
         lib/tools/ ejecuta la implementación real
              ↓
         resultado regresa a Gemini
              ↓
         Gemini genera respuesta en texto
              ↓
Browser ← { respuesta, eventoAgregado? }
```

Gemini no ejecuta funciones. Solo dice cuál llamar y con qué args. Nosotros la ejecutamos.

---

## Estructura de carpetas

```
lib/
├── gemini.ts              ← cliente Gemini + función chat()
├── tools/
│   ├── index.ts           ← junta todas las declarations y handlers
│   ├── itinerary.ts       ← tools: buscar_negocios, agregar_evento, leer_itinerario
│   ├── maps.ts            ← tools: buscar_ruta, geocodificar
│   └── calendar.ts        ← tools: Google Calendar (futuro)
├── businesses.ts          ← datos de negocios (mock → Supabase)
└── itinerary.ts           ← lógica de itinerario (mock → Supabase)
```

---

## Patrón por módulo de tools

Cada archivo en `lib/tools/` exporta dos cosas:

```ts
// lib/tools/itinerary.ts
import type { FunctionDeclaration } from '@google/genai'

export const declarations: FunctionDeclaration[] = [agregarEventoTool, leerItinerarioTool]

export const handlers: Record<string, (args: never) => unknown> = {
  agregar_evento:  (args) => agregarEvento(args),
  leer_itinerario: () => leerItinerario(),
}
```

`lib/tools/index.ts` los junta:

```ts
import * as itinerary from './itinerary'
import * as maps      from './maps'
// import * as calendar from './calendar'  ← cuando se integre

export const declarations = [
  ...itinerary.declarations,
  ...maps.declarations,
]

export const handlers: Record<string, (args: never) => unknown> = {
  ...itinerary.handlers,
  ...maps.handlers,
}
```

`lib/gemini.ts` solo importa de `index.ts`:

```ts
import { declarations, handlers } from '@/lib/tools'
```

---

## Reglas

- Agregar una tool nueva = crear o editar un archivo en `lib/tools/` + registrar en `index.ts`. `gemini.ts` no se toca.
- Cada `lib/[categoria].ts` es el único punto de acceso a sus datos. Hoy mock, mañana Supabase — nadie más cambia.
- El historial completo viaja en cada request desde el browser. Sin historial, Gemini no recuerda contexto.
- La fecha actual se inyecta en el `systemInstruction` con zona horaria `America/Mexico_City`.
- Tipar `declarations` como `FunctionDeclaration[]` — evita errores de TypeScript en build.

---

## Capas del sistema

| Capa | Archivo | Responsabilidad |
|------|---------|-----------------|
| Browser | `app/page.tsx` | UI, historial en estado, `fetch` al API route |
| Hook | `hooks/useItinerary.ts` | Sincroniza eventos con `localStorage` (offline) |
| BFF | `app/api/chat/route.ts` | Recibe request, llama `lib/gemini.ts`, devuelve respuesta |
| IA | `lib/gemini.ts` | Ciclo while de function calling |
| Tools | `lib/tools/` | Declarations + handlers por dominio |
| Datos | `lib/businesses.ts`, `lib/itinerary.ts` | Mock hoy, Supabase mañana |

---

## Regla de oro

```
Gemini no ejecuta tus funciones.
Gemini te dice cuál llamar y con qué args.
Tú la ejecutas y le devuelves el resultado.
```

---

## Cambios

| Fecha | Quién | Qué |
|-------|-------|-----|
| 2026-04-06 | Fidel | v1.0 — estructura de tools, flujo de itinerario y orden de creación |
