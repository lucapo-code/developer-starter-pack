# Git & GitHub: Control de Versiones Esencial

## 🎯 Por Qué Git

- **Historial**: Vuelve a cualquier versión anterior
- **Colaboración**: Trabaja en equipo sin sobrescribir
- **Ramificación**: Experimenta sin romper lo que funciona
- **Portafolio**: GitHub es tu CV como desarrollador

---

## Conceptos Clave

```
Working Directory → Staging Area → Local Repo → Remote Repo (GitHub)
     (tus archivos)     (git add)      (git commit)    (git push)
```

---

## Configuración Inicial

```bash
# Identidad (una sola vez)
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"

# Rama principal por defecto
git config --global init.defaultBranch main

# Editor para mensajes de commit
git config --global core.editor "code --wait"

# Alias útiles
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm "commit -m"
git config --global alias.lg "log --oneline --graph --all"
```

---

## Flujo Básico (El 90% de Lo Que Harás)

```bash
# 1. Iniciar repo nuevo
git init

# 2. Ver estado
git status

# 3. Preparar archivos (staging)
git add archivo.js          # Un archivo
git add .                   # Todo

# 4. Commit (foto del estado)
git commit -m "feat: descripcion corta"

# 5. Conectar con GitHub
git remote add origin git@github.com:usuario/repo.git

# 6. Subir
git push -u origin main
```

---

## Ramas (Branching) - La Magia de Git

```bash
# Ver ramas
git branch

# Crear y cambiar
git checkout -b feature/login

# Cambiar de rama
git checkout main

# Fusionar (desde main)
git merge feature/login

# Borrar rama fusionada
git branch -d feature/login
```

### Estrategia Simple: GitHub Flow
1. `main` = siempre deployable
2. Nueva rama por feature/fix
3. Pull Request → Review → Merge
4. Borrar rama

---

## Deshacer Cosas (¡Salvavidas!)

```bash
# Deshacer últimos cambios NO commitados
git checkout -- archivo.js        # Un archivo
git checkout -- .                 # Todo

# Deshacer último commit (mantiene cambios)
git reset --soft HEAD~1

# Deshacer último commit (borra cambios)
git reset --hard HEAD~1

# Cambiar mensaje del último commit
git commit --amend -m "nuevo mensaje"

# Ver historial
git log --oneline -10
```

---

## .gitignore - Qué NO Subir

Crea archivo `.gitignore` en la raíz:

```gitignore
# Dependencias
node_modules/
venv/
.env

# Builds
dist/
build/
*.pyc

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log

# Secrets (NUNCA subas esto)
*.pem
*.key
.env.local
```

> **Regla de oro**: Si no es código tuyo, no lo subas.

---

## Convención de Commits (Conventional Commits)

```
<tipo>[alcance]: <descripción>

[body opcional]

[footer opcional]
```

### Tipos Principales
| Tipo | Cuándo Usar |
|------|-------------|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de bug |
| `docs` | Solo documentación |
| `style` | Formato, punto y coma, etc. |
| `refactor` | Reestructurar sin cambiar comportamiento |
| `test` | Agregar/corregir tests |
| `chore` | Mantenimiento (deps, build, etc.) |

### Ejemplos
```bash
git commit -m "feat(auth): agrega login con Google"
git commit -m "fix: corrige validación de email en registro"
git commit -m "docs: actualiza README con instrucciones de deploy"
git commit -m "refactor(utils): simplifica función de fecha"
```

---

## Pull Requests (PR) - Colaboración

### Crear PR
1. Push tu rama: `git push origin feature/xyz`
2. En GitHub: "Compare & pull request"
3. Título claro: `feat: agrega autenticación JWT`
4. Descripción: qué, por qué, cómo testear
5. Reviewers: asigna a compañeros

### Buenas Prácticas PR
- **Pequeños**: < 400 líneas si es posible
- **Un tema**: Una feature/fix por PR
- **Tests**: Incluye tests para lo nuevo
- **Descripción**: Qué cambia, cómo probar, screenshots si UI

---

## Resolución de Conflictos

```bash
# 1. Actualiza main
git checkout main && git pull

# 2. Trae cambios a tu rama
git checkout feature/mi-rama
git merge main

# 3. Si hay conflictos:
# - Abre archivos marcados
# - Busca >>>>>>> ======= <<<<<<<
# - Edita para resolver
# - git add .
# - git commit (sin -m, usa editor)

# 4. Push
git push origin feature/mi-rama
```

---

## Comandos Avanzados Útiles

```bash
# Ver diferencias
git diff                    # Working vs Staging
git diff --staged           # Staging vs Last Commit
git diff main..feature/xyz  # Entre ramas

# Buscar en historial
git log --grep="login"      # Por mensaje
git log -S "funcionX"       # Por código
git log --author="Tu Nombre"

# Stash (guardar temporal)
git stash                   # Guarda cambios
git stash pop               # Recupera y borra stash
git stash list              # Ver stashes

# Tags (versiones)
git tag v1.0.0
git push origin v1.0.0

# Rebase (limpiar historial antes de PR)
git rebase -i main          # Interactivo
```

---

## Cheatsheet Visual

```
┌─────────────────────────────────────────────────────────────┐
│                    GIT CHEATSHEET                           │
├─────────────────────────────────────────────────────────────┤
│  git init              # Iniciar repo                       │
│  git clone <url>       # Clonar repo                        │
│  git status            # Estado                             │
│  git add <archivo>     # Stage archivo                      │
│  git add .             # Stage todo                         │
│  git commit -m "msg"   # Commit                             │
│  git push              # Subir                              │
│  git pull              # Bajar                              │
│  git branch            # Ver ramas                          │
│  git checkout -b <n>   # Crear y cambiar rama               │
│  git merge <rama>      # Fusionar                           │
│  git log --oneline     # Historial corto                    │
│  git diff              # Cambios sin stage                   │
│  git stash             # Guardar temporal                   │
│  git reset --soft HEAD~1  # Deshacer commit (keep changes)  │
└─────────────────────────────────────────────────────────────┘
```

---

## Ejercicio Práctico

1. Crea repo `git-practice`
2. Haz 3 commits en `main`: `feat: archivo A`, `feat: archivo B`, `fix: typo`
3. Crea rama `feature/nueva`, haz 2 commits
4. Vuelve a `main`, haz 1 commit más
5. Fusiona `feature/nueva` → `main` (resuelve conflicto si hay)
6. Borra la rama
7. Sube a GitHub

---

## Recursos

- [Git Handbook (GitHub)](https://guides.github.com/introduction/git-handbook/)
- [Oh Shit, Git!?](https://ohshitgit.com/) - Soluciones a problemas comunes
- [Learn Git Branching](https://learngitbranching.js.org/) - Visual e interactivo
- [Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/)