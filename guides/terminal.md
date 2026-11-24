# Terminal & Línea de Comandos: Tu Superpoder

## 🎯 Por Qué La Terminal

- **Velocidad**: 10x más rápido que GUI para muchas tareas
- **Automatización**: Scripts que hacen trabajo repetitivo
- **Servidores**: No hay interfaz gráfica en producción
- **Herramientas modernas**: Git, npm, Docker, Kubernetes viven aquí

---

## Navegación Básica

```bash
pwd                    # ¿Dónde estoy?
ls                     # Listar archivos
ls -la                 # Con ocultos y detalles
cd /ruta/absoluta      # Ir a ruta absoluta
cd ~                   # Ir a home
cd ..                  # Subir un nivel
cd -                   # Directorio anterior
```

### Atajos de Ruta
| Símbolo | Significado |
|---------|-------------|
| `~` | Home (`/home/usuario` o `/data/data/com.termux/files/home`) |
| `.` | Directorio actual |
| `..` | Directorio padre |
| `-` | Directorio anterior |
| `*` | Comodín (cualquier cosa) |

---

## Gestión de Archivos

```bash
# Crear
mkdir carpeta                 # Una carpeta
mkdir -p a/b/c                # Anidadas
touch archivo.txt             # Archivo vacío

# Copiar
cp origen.txt destino.txt     # Archivo
cp -r carpeta/ destino/       # Carpeta recursivo

# Mover / Renombrar
mv viejo.txt nuevo.txt        # Renombrar
mv archivo.txt carpeta/       # Mover

# Borrar
rm archivo.txt                # Archivo
rm -r carpeta/                # Carpeta (¡cuidado!)
rm -rf carpeta/               # Fuerza (¡PELIGRO!)
```

> **⚠️ `rm -rf` no tiene papelera. Piensa antes de ejecutar.**

---

## Ver Contenido

```bash
cat archivo.txt               # Todo el contenido
less archivo.txt              # Paginado (q para salir)
head -n 20 archivo.txt        # Primeras 20 líneas
tail -n 20 archivo.txt        # Últimas 20 líneas
tail -f archivo.log           # Seguir en tiempo real (logs)
```

---

## Buscar (Muy Potente)

```bash
# Archivos por nombre
find . -nombre "*.js"         # Extensión .js en actual y subcarpetas
find /ruta -type f -name "*.py"  # Solo archivos .py

# Contenido dentro de archivos
grep "texto" archivo.txt              # Línea con "texto"
grep -r "funcion" .                   # Recursivo en carpeta actual
grep -i "error" archivo.log           # Ignora mayúsculas
grep -n "TODO" *.js                   # Con número de línea
grep -v "console.log" archivo.js      # Líneas que NO contienen

# Combinar: buscar TODO en .js sin node_modules
grep -r "TODO" --include="*.js" --exclude-dir=node_modules .
```

---

## Permisos y Propietario

```bash
# Ver permisos
ls -l
# -rw-r--r--  1 user group  123 ene 1 10:00 archivo.txt
#  ^  ^  ^  ^  ^  ^
#  |  |  |  |  |  └── Otros (r=leer)
#  |  |  |  |  └──── Grupo (r=leer)
#  |  |  |  └──────── Dueño (rw=leer/escribir)
#  |  |  └─────────── Tipo (-=archivo, d=directorio, l=enlace)

# Cambiar permisos
chmod +x script.sh            # Ejecutable
chmod 755 script.sh           # rwxr-xr-x (dueño todo, otros leer/ejecutar)
chmod 644 archivo.txt         # rw-r--r-- (estándar archivo)

# Cambiar propietario (root)
sudo chown user:group archivo
```

---

## Variables de Entorno

```bash
# Ver todas
env
printenv

# Ver una
echo $HOME
echo $PATH

# Definir temporal (solo esta sesión)
export MI_VAR="valor"
export PATH="$PATH:/nueva/ruta"

# Definir permanente (añade a ~/.bashrc o ~/.zshrc)
echo 'export MI_VAR="valor"' >> ~/.bashrc
source ~/.bashrc
```

---

## Redirección y Pipes (La Magia Unix)

```bash
# Redirigir salida
comando > archivo.txt         # Sobrescribe
comando >> archivo.txt        # Añade (append)
comando 2> errores.txt        # Solo errores (stderr)
comando &> todo.txt           # Salida + errores

# Pipe: salida de A = entrada de B
ls -la | grep ".txt"          # Solo .txt
cat archivo.log | grep "ERROR" | wc -l   # Contar líneas con ERROR
history | grep git            # Buscar en historial

# Combinar
grep -r "TODO" . --include="*.js" | head -20
```

---

## Procesos

```bash
# Ver procesos
ps aux                        # Todos
top                           # Interactivo (q para salir)
htop                          # Mejor (instalar: pkg install htop)

# Matar proceso
kill PID                      # Suave (SIGTERM)
kill -9 PID                   # Fuerza (SIGKILL)
pkill nombre                  # Por nombre
killall nombre                # Todos con ese nombre

# Background / Foreground
comando &                     # Ejecutar en background
jobs                          # Ver jobs en background
fg %1                         # Traer job 1 a foreground
bg %1                         # Reanudar job 1 en background
Ctrl+Z                        # Pausar proceso actual (luego bg)
nohup comando &               # Inmune a cerrar terminal
```

---

## Historial y Autocompletado

```bash
history                       # Ver historial
history | grep "git"          # Buscar en historial
!!                            # Último comando
!git                          # Último que empieza con "git"
Ctrl+R                        # Buscar interactivo (escribe para filtrar)
Tab                           # Autocompletar (archivos, comandos)
Tab Tab                       # Mostrar opciones
```

---

## Atajos de Teclado Esenciales

| Atajo | Acción |
|-------|--------|
| `Ctrl+A` | Inicio de línea |
| `Ctrl+E` | Final de línea |
| `Ctrl+U` | Borrar desde cursor al inicio |
| `Ctrl+K` | Borrar desde cursor al final |
| `Ctrl+W` | Borrar palabra anterior |
| `Ctrl+L` | Limpiar pantalla |
| `Ctrl+C` | Cancelar comando actual |
| `Ctrl+D` | Salir (EOF) |
| `↑/↓` | Historial anterior/siguiente |
| `Ctrl+R` | Buscar en historial |

---

## Herramientas Modernas (Instala Estas)

```bash
# Mejor ls
pkg install eza        # o: cargo install eza
alias ls="eza --icons"
alias ll="eza -la --icons"
alias tree="eza --tree"

# Mejor find
pkg install fd         # o: cargo install fd-find

# Mejor grep
pkg install ripgrep    # rg "patrón" .

# Mejor cat
pkg install bat        # bat archivo.txt

# Mejor top
pkg install htop       # o: btop

# Fuzzy finder (¡cambia la vida!)
pkg install fzf
# Ctrl+T = buscar archivos, Ctrl+R = historial mejorado
```

---

## Scripts Básicos (Bash)

```bash
#!/bin/bash
# mi-script.sh

# Variables
NOMBRE="Mundo"
FECHA=$(date)

# Argumentos
echo "Hola $1!"         # $1 = primer argumento
echo "Todos: $@"        # Todos los argumentos

# Condicional
if [ -f "archivo.txt" ]; then
    echo "Existe"
else
    echo "No existe"
fi

# Bucle
for archivo in *.txt; do
    echo "Procesando $archivo"
done

# Función
saludar() {
    echo "Hola, $1!"
}
saludar "Desarrollador"
```

### Hacerlo Ejecutable y Usarlo
```bash
chmod +x mi-script.sh
./mi-script.sh "Lucas"
# O desde cualquier lado (añade a PATH):
mv mi-script.sh ~/bin/saludar
saludar "Lucas"
```

---

## Termux Específico (Android)

```bash
# Acceso a almacenamiento
termux-setup-storage
# Crea ~/storage/{downloads,dcim,music,pictures,movies,documents}

# API de Android (requiere app Termux:API)
termux-notification -t "Título" -c "Contenido"
termux-tts-speak "Hola desde la terminal"
termux-battery-status
termux-wifi-scaninfo
termux-location

# Despertar CPU (evita sleep)
termux-wake-lock
termux-wake-unlock

# Paquetes útiles
pkg install git nodejs python ffmpeg neovim htop fzf ripgrep bat eza
```

---

## Cheatsheet Rápido

```
┌────────────────────────────────────────────────────────────────┐
│ NAVEGACIÓN          ARCHIVOS          BÚSQUEDA                 │
│ pwd                 cp -r             grep -r "patrón" .       │
│ ls -la              mv                find . -name "*.js"      │
│ cd ~                rm -rf            rg "patrón"              │
│ cd -                touch             fd "patrón"              │
├────────────────────────────────────────────────────────────────┤
│ VER                 PERMISOS          PROCESOS                 │
│ cat / less          chmod +x          ps aux / htop            │
│ head / tail -f      chmod 755         kill -9 PID              │
│ bat                 chown             nohup cmd &              │
├────────────────────────────────────────────────────────────────┤
│ PIPES               HISTORIAL         ATAJOS                   │
│ cmd > out           history           Ctrl+A/E (inicio/fin)    │
│ cmd \| grep          Ctrl+R            Ctrl+U/K (borrar)       │
│ cmd &> all          !!                Ctrl+C (cancelar)        │
└────────────────────────────────────────────────────────────────┘
```

---

## Ejercicios Prácticos

1. **Navega**: `cd ~`, crea `practica/`, entra, crea `hola.txt`
2. **Busca**: `grep -r "TODO" ~/proyectos --include="*.js"`
3. **Pipe**: `ls -la | grep "^d" | wc -l` (cuenta carpetas)
4. **Script**: Crea `~/bin/backup.sh` que comprima una carpeta con fecha
5. **Alias**: Añade `alias ll="eza -la"` a tu `.bashrc`

---

## Siguiente Paso

→ [Git & GitHub](git-github.md) - Control de versiones
→ [HTML & CSS](html-css.md) - Tu primera web