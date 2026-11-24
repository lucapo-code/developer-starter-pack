# Git Cheatsheet

## Configuración
```bash
git config --global user.name "Nombre"
git config --global user.email "email@dominio.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm "commit -m"
git config --global alias.lg "log --oneline --graph --all"
```

## Inicio
```bash
git init                          # Nuevo repo
git clone <url>                   # Clonar
git clone <url> mi-carpeta        # Clonar en carpeta específica
```

## Estado y Archivos
```bash
git status                        # Estado actual
git status -s                     # Corto
git diff                          # Cambios sin stage
git diff --staged                 # Cambios en stage
git diff HEAD                     # Todos los cambios
```

## Stage y Commit
```bash
git add archivo.js                # Un archivo
git add .                         # Todo
git add -p                        # Interactivo (parches)
git reset archivo.js              # Unstage
git commit -m "mensaje"           # Commit
git commit -am "mensaje"          # Add + commit (tracked only)
git commit --amend                # Editar último commit
git commit --amend --no-edit      # Mantener mensaje
```

## Historial
```bash
git log                           # Completo
git log --oneline                 # Una línea
git log --oneline -10             # Últimos 10
git log --graph --all             # Gráfico ramas
git log --grep="fix"              # Buscar en mensaje
git log -S "funcion"              # Buscar en código
git show <hash>                   # Detalle commit
git blame archivo.js              # Quién escribió cada línea
```

## Ramas
```bash
git branch                        # Listar
git branch -a                     # Todas (remotas también)
git branch nueva-rama             # Crear
git checkout -b nueva-rama        # Crear y cambiar
git switch -c nueva-rama          # Moderno (Git 2.23+)
git checkout main                 # Cambiar a main
git switch main                   # Moderno
git branch -d rama                # Borrar (fusionada)
git branch -D rama                # Forzar borrado
git merge rama                    # Fusionar en actual
git rebase main                   # Rebase sobre main
```

## Remoto
```bash
git remote -v                     # Ver remotos
git remote add origin <url>       # Agregar
git remote set-url origin <url>   # Cambiar URL
git push -u origin main           # Primera subida (set upstream)
git push                          # Subir (con upstream)
git push origin rama              # Subir rama específica
git push --force-with-lease       # Force push seguro
git pull                          # Bajar + merge
git pull --rebase                 # Bajar + rebase
git fetch                         # Solo bajar metadatos
git fetch --prune                 # Limpiar remotos borrados
```

## Deshacer
```bash
git checkout -- archivo.js        # Descartar cambios (working dir)
git restore archivo.js            # Moderno
git reset --soft HEAD~1           # Deshacer commit, keep changes
git reset --mixed HEAD~1          # Deshacer commit + unstage (default)
git reset --hard HEAD~1           # Deshacer commit + borrar cambios
git revert <hash>                 # Commit inverso (seguro para shared)
git clean -fd                     # Borrar untracked files/dirs
```

## Stash
```bash
git stash                         # Guardar cambios
git stash push -m "mensaje"       # Con mensaje
git stash list                    # Ver stashes
git stash pop                     # Aplicar y borrar
git stash apply                   # Aplicar (keep stash)
git stash drop                    # Borrar stash
git stash clear                   # Borrar todos
```

## Tags
```bash
git tag                           # Listar
git tag v1.0.0                    # Ligero
git tag -a v1.0.0 -m "Release"    # Anotado
git push origin v1.0.0            # Subir tag
git push origin --tags            # Subir todos
git tag -d v1.0.0                 # Borrar local
git push origin --delete v1.0.0   # Borrar remoto
```

## Búsqueda
```bash
git log --oneline --grep="bug"
git log -S "TODO" --oneline
git grep "funcion"                # En working dir
git grep "funcion" HEAD           # En último commit
```

## Submódulos
```bash
git submodule add <url> ruta      # Agregar
git submodule update --init       # Inicializar (tras clone)
git submodule update --remote     # Actualizar
```

## Aliases Útiles (añade a ~/.gitconfig)
```ini
[alias]
    lg = log --oneline --graph --all --decorate
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = !gitk
    amend = commit --amend --no-edit
    wip = commit -am "WIP"
    unwip = reset HEAD~1
    cleanup = "!git branch --merged | grep -v '\\*\\|main\\|master' | xargs -n 1 git branch -d"
```