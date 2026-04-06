# MexGo — Entorno de desarrollo
**Los Mossitos · Genius Arena 2026**

Setup desde cero. En orden. No saltarse pasos.
Tiempo estimado: ~30–45 min primera vez.

---

## 0. Leer `.md` en Windows (antes de todo)

Los docs del proyecto son `.md`. Para leerlos bonito en terminal:

**Opción rápida — sin instalar nada:**
Abrir el `.md` directo en VS Code. Se renderiza con `Ctrl+Shift+V`.

**Opción terminal — Glow:**
Se instala en el paso de WSL. Ver sección 1.

---

## 1. WSL — Windows Subsystem for Linux

Necesario para que Node, Git y las herramientas del proyecto funcionen igual que en Mac/Linux y no haya sorpresas entre máquinas.

**Instalar:**
```powershell
# Abrir PowerShell como Administrador y correr:
wsl --install
```
Reiniciar cuando pida. Al volver, abre "Ubuntu" desde el menú de inicio.
Crear usuario y contraseña cuando lo pida (no tiene que ser la misma de Windows).

**Verificar:**
```bash
wsl --version
```

> Todo lo que sigue se corre dentro de la terminal de Ubuntu, no en PowerShell.

---

## 2. Node.js

No instalar desde la página oficial directo — usar `nvm` para poder cambiar versiones sin problema.

```bash
# Instalar nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# Recargar terminal
source ~/.bashrc

# Instalar Node LTS
nvm install --lts
nvm use --lts

# Verificar
node -v   # debe mostrar v20.x.x o superior
npm -v
```

---

## 3. Git

```bash
# Instalar
sudo apt update && sudo apt install git -y

# Configurar con tus datos
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"

# Verificar
git --version
```

**Conectar con GitHub (SSH — recomendado):**
```bash
ssh-keygen -t ed25519 -C "tu@email.com"
# Enter x3 para aceptar defaults

cat ~/.ssh/id_ed25519.pub
# Copiar el output
```
Ir a GitHub → Settings → SSH Keys → New SSH Key → pegar.

---

## 4. Glow — leer `.md` en terminal

```bash
# Instalar
sudo apt install snapd -y
sudo snap install glow

# Usar
glow DOCUMENTO.md
glow docs/ARQUITECTURA.md

# Navegar todos los .md del proyecto
glow .
```

---

## 5. VS Code + WSL

Instalar VS Code en Windows normal desde [code.visualstudio.com](https://code.visualstudio.com).

Luego instalar la extensión **WSL** (de Microsoft) en VS Code.

Para abrir el proyecto desde WSL:
```bash
# Dentro de Ubuntu, en la carpeta del proyecto:
code .
```
VS Code se abre en Windows pero conectado a WSL. Todo funciona como en Linux.

**Extensiones obligatorias** (instalar dentro de VS Code con WSL activo):

| Extensión | ID |
|---|---|
| Tailwind CSS IntelliSense | `bradlc.vscode-tailwindcss` |
| Prettier | `esbenp.prettier-vscode` |
| ESLint | `dbaeumer.vscode-eslint` |
| TypeScript (ya viene) | — |

Activar `Format on Save`:
`Ctrl+Shift+P` → "Open User Settings (JSON)" → agregar:
```json
"editor.formatOnSave": true,
"editor.defaultFormatter": "esbenp.prettier-vscode"
```

---

## 7. Verificación final

Si todo está bien, estos comandos no deben tirar error:

```bash
node -v       # v20+
npm -v        # v10+
git --version # 2.x
glow --version
code --version
```

Y `npm run dev` levanta sin errores.

---

## Problemas comunes

**`wsl --install` dice que ya está instalado pero no funciona:**
```powershell
wsl --update
wsl --set-default-version 2
```

**`nvm: command not found` después de instalarlo:**
```bash
source ~/.bashrc
# o cerrar y reabrir la terminal de Ubuntu
```

**VS Code no ve la extensión WSL:**
Cerrar VS Code, abrir Ubuntu, correr `code .` desde ahí.

**`npm install` falla con permisos:**
```bash
# Nunca usar sudo con npm. Si pasa, arreglar permisos:
npm config set prefix ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

---


## Eso dejenlo al final :)

```bash
# En Ubuntu, elegir dónde guardar (recomendado: carpeta home)
cd ~
git clone git@github.com:los-mossitos/mexgo.git
cd mexgo

# Instalar dependencias
npm install

# Copiar variables de entorno
cp .env.example .env.local
# Llenar .env.local con los valores reales (pedírselos a Fidel)

# Levantar
npm run dev
```
Abrir [http://localhost:3000](http://localhost:3000).

---

