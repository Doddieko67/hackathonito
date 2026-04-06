# MexGo — Roles y Preparación
**Los Mossitos · Genius Arena 2026**

Responsabilidades estrictas y mini-proyectos de preparación pre-hackathon.

## Distribución general
| Quién | Rol principal | Tecnologías clave |
|---|---|---|
| Fidel | Arquitecto / IA / Algoritmo | Next.js API Routes, Gemini, AWS |
| Emi | Frontend Turista | Tailwind, Mapbox, PWA Cache |
| Farid | Frontend Admin | Tailwind, Supabase Auth, Cloudinary |
| Alan | Backend / BD | Supabase, PostgreSQL, Next.js API |
| Xavier | QA Global / Integrador | Postman, UI Polish, E2E, Datos de Impacto |

## Fidel
**Responsabilidad:** Arquitectura, algoritmo `lib/equity.ts`, Gemini, despliegue AWS.
**Preparación:**
- Crear repositorio Next.js con ruta `/api/recomendar`.
- Conectar a Gemini Flash para devolver itinerarios en JSON estructurado.
- Desplegar API funcional en AWS (capa gratuita).

## Emi
**Responsabilidad:** Flujo del turista, mapas, tarjetas, modo offline.
**Preparación:**
- Construir UI móvil Mobile-First con Tailwind CSS.
- Integrar mapa (Mapbox) con tarjetas deslizables.
- Implementar persistencia en `localStorage` para simular modo offline.

## Farid
**Responsabilidad:** Panel admin, autenticación, gestión de imágenes.
**Preparación:**
- Crear flujo de `/login` con Supabase Auth.
- Implementar subida de archivos a Cloudinary y renderizado de respuesta.
- Diseñar dashboard administrativo básico para gestión de negocios.

## Alan
**Responsabilidad:** Infraestructura de BD, esquema SQL, endpoints, tiempo real.
**Preparación:**
- Definir esquema SQL relacional en Supabase.
- Construir API Routes con operaciones CRUD completas.
- Configurar `Supabase Realtime` para actualizaciones de saturación en vivo.

## Xavier (Rol Ampliado: El "Ojo del Jurado")
**Responsabilidad:** Calidad integral, curaduría de datos, pulido de interfaz y blindaje de la demo.
**Funciones clave:**
- **Curaduría de Impacto:** Crear datos semilla (JSON) que no sean solo "texto falso", sino negocios reales con descripciones atractivas y fotos que luzcan el proyecto.
- **Vigilancia de Contratos:** Validar que el Backend (Alan) y los Frontends coincidan exactamente con lo definido en `API.md`.
- **QA End-to-End:** Probar el flujo completo como si fuera un turista real (QR -> Itinerario -> Offline) detectando errores de lógica.
- **Pulido UI/UX:** Intervenir en el código para ajustar márgenes, tipografías y colores de marca, asegurando que la app se vea "pixel-perfect".
- **Guardián del Golden Path:** Asegurar que la ruta específica de la demostración funcione al 100% y decretar el "congelamiento de código" antes del pitch final.

**Preparación:**
- Script generador de 50 negocios verosímiles con coordenadas y sello "Ola México".
- Dominar Postman para estresar los endpoints del backend.
- Repaso intensivo de Tailwind CSS para realizar ajustes visuales rápidos sin romper la estructura.

## 6. Reporte de Errores y QA (Flujo con Xavier)
- ✗ Prohibido enviar mensajes al chat diciendo "no funciona", "se rompió" o "tira error" sin contexto.
- → Formato obligatorio para reportar un bug a Xavier:
    1. **Dónde:** Ruta exacta, vista o endpoint (ej. `/api/recomendar` o vista móvil del mapa).
    2. **Pasos:** Qué hiciste exactamente antes de que fallara (1. Entré a X, 2. Toqué Y).
    3. **Qué esperabas vs. Qué pasó:** "Debía mostrar 4 tarjetas, pero mostró un error 500".
    4. **Evidencia:** Captura de pantalla o el log exacto de la terminal pegado.
- → Regla de Triage (Clasificación): 
    - Si el bug rompe el *Golden Path* (el flujo de la demostración a los jueces): **Prioridad Máxima**, el desarrollo se frena hasta arreglarlo.
    - Si es un botón secundario o un margen mal alineado: Se anota en la lista de "Deuda Técnica / Post-Hackathon" y el desarrollo sigue.

---

## Cambios
| Fecha | Quién | Qué |
|---|---|---|
| 2026-03-31 | IA | v1.1 — Expansión de Xavier a QA Global e Integrador. |
