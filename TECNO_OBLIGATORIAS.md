# MexGo — Tecnologías Obligatorias
**Los Mossitos · Genius Arena 2026**

Reglas base de entorno y herramientas. Nadie empieza a codear sin esto.

## 1. Terminal y Entorno
- Instalar Glow para leer `.md` en terminal sin salir del flujo de trabajo.
- ? **Gestor de paquetes** — `npm`, `yarn` o `pnpm` por definir. Usar solo el elegido para evitar conflictos en el lockfile.
- Comandos base: `npm run dev` para levantar entorno local, `cd` y `ls`/`dir`.

## 2. Git
- ✗ Prohibido hacer `push` directo a la rama `main`.
- → Crear ramas con prefijo funcional: `feat/tarjetas` o `fix/auth`.
- Ciclo de trabajo diario: `git pull origin main` → rama nueva → `add .` → `commit` claro → `push` → Avisar antes de un Merge.

## 3. Uso de IA (Context Engineering)
- ✗ Cero web. Usar puro Claude Code o Terminal
- → Inyección obligatoria del bloque "Contexto mínimo" de `CONTEXT_ENGINEERING.md y pasar MEXGO.pdf` antes de pedir código.
- La IA no toma decisiones de arquitectura. Si propone saltar el patrón BFF, se descarta.

## 4. Editor (VS Code)
| Extensión | Para qué |
|---|---|
| Tailwind CSS IntelliSense | Autocompletado exacto de clases |

- → Obligatorio activar `Format on Save` para eliminar conflictos de Git por espacios.

## 5. Reglas inquebrantables
- ✗ El frontend nunca llama a Gemini ni a Supabase directamente.
- → El frontend solo consume endpoints de `/app/api/`.
- ✗ Claves secretas (`GEMINI_API_KEY`, `SUPABASE_SERVICE_ROLE_KEY`) nunca llevan el prefijo `NEXT_PUBLIC_`. No subir `.env.local`.

---

## Cambios
| Fecha | Quién | Qué |
|---|---|---|
| 2026-03-31 | IA | v1.0 — creación del documento. |
