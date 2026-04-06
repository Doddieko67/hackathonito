# MexGo — Context Engineering
**Los Mossitos · Genius Arena 2026**

---

## Qué es esto

Guía para usar IA (Claude, Gemini, etc.) en el desarrollo de MexGo.
Cómo darle co.mdto, qué esperar, qué no hacer.

---

## Estilo de documentación del proyecto

**Regla principal:** corto, directo, sin relleno.

- Una idea = un párrafo o una línea. No más.
- Sin introducción, sin conclusión, sin "como se mencionó anteriormente".
- Si se sobreentiende, no se escribe.
- Verbos directos: "llama a Gemini", "devuelve tarjetas", no "se procede a realizar una llamada".
- Veredictos explícitos: `✓ Confirmado`, `✗ Descartado`, `? Pendiente`, `→ Decisión`.
- Tablas > listas cuando hay más de 3 ítems con estructura similar.
- Código > descripción cuando se puede.

**Por qué:** las IAs no necesitan prosa para entender contexto — necesitan
densidad. Los humanos tampoco tienen tiempo de leer informes. Ambos ganan
con el mismo estilo.

---

## Cómo darle co.mdto a la IA

### Contexto mínimo para cualquier sesión

Pegar al inicio de la conversación:

```
Proyecto: MexGo — PWA turística con algoritmo de equidad para micronegocios.
Stack: Next.js 15, Supabase, Gemini Flash, Mapbox, Tailwind, TypeScript estricto.
Patrón: monolito modular + BFF con Next.js API Routes.
Regla crítica: frontend nunca llama a Supabase ni Gemini directo — solo /app/api/.
Algoritmo de equidad solo en lib/equity.ts — nunca en el cliente.
Docs: DOCUMENTO.md (raíz)
```

### Contexto por módulo

Agregar según lo que se esté trabajando:

**Algoritmo / lib:**
```
Módulo: lib/equity.ts
Responsable: Fidel
Score = (relevancia × proximidad) + βOlaMéxico − γsaturación
β = bonus fijo negocios verificados Ola México
γ = penalización creciente por saturación del día
Desempate: negocio con más tiempo sin cliente referido.
Resultado: 4–6 negocios ordenados. Nunca uno solo.
Ver ALGORITMO.md para pseudocódigo completo.
```

**Frontend turista:**
```
Módulo: app/(turista)/ + components/turista/
Responsable: Emi
Flujo: bienvenida → cuestionario → mapa con tarjetas deslizables.
Mapas: Mapbox GL JS.
Sin estrellas, sin reseñas, sin precios. Solo señales sociales.
Offline: itinerario en localStorage.
Ver FRONTEND_TURISTA.md.
```

**Frontend admin:**
```
Módulo: app/(admin)/ + components/admin/
Responsable: Farid
Flujo: login → dashboard → alta/edición de negocio.
Auth: Supabase Auth.
Subida de fotos: Cloudinary.
Ver FRONTEND_ADMIN.md.
```

**Backend / Supabase:**
```
Módulo: app/api/ + lib/supabase.ts + supabase/migrations/
Responsable: Alan
Endpoints: /api/recomendar, /api/itinerario, /api/negocios, /api/auth.
BD: PostgreSQL en Supabase. Schema en SCHEMA.md.
Tiempo real: Supabase Realtime para actualizar scores de saturación.
```

**Gemini / IA:**
```
Módulo: lib/gemini.ts + lib/cultural.ts + app/api/itinerario/
Responsable: Fidel
Gemini Flash — dos usos: perfil cultural por país + generación de itinerario.
cultural.ts: país → {precios, distancia caminata, afinidades}
itinerario: bloques horarios + perfil + partido (opcional) → plan de días.
Ver IA.md.
```

---

## Qué pedirle a la IA

**Sí funciona bien:**
- "Implementa `lib/equity.ts` con esta firma: ..." + pegar el pseudocódigo de ALGORITMO.md
- "Revisa este endpoint, el frontend recibe X pero espera Y"
- "Escribe el migration SQL para esta tabla" + pegar el schema de SCHEMA.md
- "Refactoriza este componente para que no importe Supabase directo"
- "Actualiza ARQUITECTURA.md con esta decisión: ..."

**No funciona bien / evitar:**
- Pedirle que "diseñe toda la arquitectura" sin co.mdto — ya está decidida.
- Pedirle que elija tecnologías — ya están confirmadas.
- Sesiones largas sin re-inyectar co.mdto mínimo — la IA olvida.
- Pedirle que escriba docs largos y formales — usar el estilo cuaderno de este proyecto.

---

## Cómo actualizar los docs con IA

Cuando se tome una decisión, pedirle:

```
Actualiza [DOCUMENTO.md / ARQUITECTURA.md / etc.] con esta decisión:
[descripción corta]
Estilo: cuaderno — directo, sin relleno, una idea por línea.
```

La IA debe agregar la decisión a la sección correcta y registrarla
en el historial de cambios con la fecha y el autor.

---

## Reglas para la IA al escribir docs del proyecto

1. Estilo cuaderno — ver sección "Estilo de documentación".
2. Sin portadas formales — título + equipo + fecha en una línea.
3. Veredictos explícitos en cada decisión (`✓ / ✗ / ? / →`).
4. Tablas para comparaciones, listas para pasos o reglas.
5. Código para todo lo que se pueda expresar como código.
6. Historial de cambios al final — fecha, quién, qué. Una línea por cambio.
7. Nunca duplicar info que ya está en otro `.md` — solo referenciar.

---

## Variables de entorno — referencia rápida

```
# Servidor únicamente — nunca NEXT_PUBLIC_
SUPABASE_SERVICE_ROLE_KEY=
GEMINI_API_KEY=
MAPBOX_SECRET_TOKEN=
DEEPL_API_KEY=
CLOUDINARY_API_SECRET=

# Cliente — solo estas
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
NEXT_PUBLIC_MAPBOX_TOKEN=
```

---

*Última actualización: Fidel — v1.0*
