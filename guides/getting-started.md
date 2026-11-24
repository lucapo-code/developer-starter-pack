# Guía de Inicio: Tu Primer Día Como Desarrollador

## 🎯 Objetivo
Configurar tu entorno y escribir tu primer "Hola Mundo" en 30 minutos.

---

## 1. Elige Tu Editor de Código

| Editor | Pros | Contras | Ideal Para |
|--------|------|---------|------------|
| **VS Code** | Extensiones, Git integrado, gratis | Pesado | Todo |
| **Cursor** | IA integrada, basado en VS Code | De pago para pro | IA-assisted coding |
| **Neovim** | Ligero, rápido, terminal | Curva de aprendizaje | Terminal lovers |
| **Zed** | Rápido, colaborativo, Rust | Solo macOS/Linux | Performance |

> **Recomendación**: Empieza con **VS Code**. Instala la extensión "Spanish Language Pack".

---

## 2. Instala Lo Básico

### Windows
```powershell
# Chocolatey (gestor de paquetes)
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Herramientas esenciales
choco install git nodejs python vscode -y
```

### macOS
```bash
# Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Herramientas
brew install git node python code
```

### Linux (Ubuntu/Debian)
```bash
sudo apt update && sudo apt install -y git nodejs npm python3 code
```

### Termux (Android)
```bash
pkg install git nodejs python code
```

---

## 3. Configura Git (Una Sola Vez)

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"
```

Verifica:
```bash
git config --list
```

---

## 4. Crea Tu Cuenta de GitHub

1. Ve a [github.com](https://github.com) y regístrate
2. Verifica tu email
3. Configura **SSH** (recomendado) o usa **HTTPS** con token

### SSH (Más Seguro)
```bash
ssh-keygen -t ed25519 -C "tu@email.com"
cat ~/.ssh/id_ed25519.pub  # Copia esto
# Ve a GitHub → Settings → SSH Keys → New SSH Key → Pégalo
```

---

## 5. Tu Primer Repositorio

```bash
# Crear carpeta del proyecto
mkdir mi-primer-proyecto && cd mi-primer-proyecto

# Inicializar Git
git init

# Crear archivo
echo "console.log('¡Hola, mundo!');" > index.js

# Primer commit
git add .
git commit -m "feat: primer commit - hola mundo"

# Conectar con GitHub (reemplaza TU_USUARIO)
git remote add origin git@github.com:TU_USUARIO/mi-primer-proyecto.git
git push -u origin main
```

---

## 6. Estructura Básica de un Proyecto

```
mi-proyecto/
├── .git/              # Historial de Git (oculto)
├── .gitignore         # Qué NO subir a Git
├── README.md          # Documentación del proyecto
├── package.json       # Dependencias (Node.js)
├── src/               # Código fuente
│   └── index.js
├── tests/             # Pruebas
└── docs/              # Documentación extra
```

---

## 7. Comandos Git Esenciales (Cheatsheet)

| Comando | Qué Hace |
|---------|----------|
| `git status` | Estado de archivos |
| `git add .` | Preparar todo para commit |
| `git commit -m "msg"` | Guardar cambios |
| `git push` | Subir a GitHub |
| `git pull` | Bajar cambios |
| `git log --oneline` | Historial resumido |
| `git branch` | Ver ramas |
| `git checkout -b rama` | Crear y cambiar rama |
| `git merge rama` | Fusionar rama |

---

## 8. Flujo de Trabajo Diario

```bash
# 1. Antes de empezar
git pull origin main

# 2. Crear rama para tu tarea
git checkout -b feature/nueva-funcionalidad

# 3. Trabajar, commitear seguido
git add .
git commit -m "feat: agrega login de usuario"

# 4. Subir tu rama
git push origin feature/nueva-funcionalidad

# 5. En GitHub: abre Pull Request → Review → Merge

# 6. Limpiar
git checkout main
git pull origin main
git branch -d feature/nueva-funcionalidad
```

---

## 9. Recursos Para Seguir Aprendiendo

### Gratuitos y en Español
- [GitHub Skills](https://skills.github.com/) - Cursos interactivos
- [freeCodeCamp](https://www.freecodecamp.org/espanol/) - Currículo completo
- [Midudev](https://midu.dev/) - Blog y YouTube excelente
- [Manz.dev](https://manz.dev/) - Cursos web gratis
- [Documentación MDN](https://developer.mozilla.org/es/) - La biblia web

### Canales YouTube
- **Midudev** - React, JS, carrera
- **Fazt** - Tutoriales prácticos
- **Código Facilito** - Fundamentos
- **HolaMundo** - Python, backend

---

## ✅ Checklist de Tu Primer Día

- [ ] Editor instalado y configurado
- [ ] Git instalado y configurado
- [ ] Cuenta GitHub creada
- [ ] SSH configurado (o token HTTPS)
- [ ] Primer repo creado y subido
- [ ] Entiendes: `add`, `commit`, `push`, `pull`
- [ ] Sabes abrir la terminal en tu editor

---

## 🎉 ¡Ya Eres Desarrollador!

> "El código se escribe una vez, se lee muchas veces. Escribe para que otros (y tú del futuro) lo entiendan."

**Siguiente paso**: [Terminal & Línea de Comandos](terminal.md) →