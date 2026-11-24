# Terminal Cheatsheet

## Navegación
```bash
pwd                           # Directorio actual
ls                            # Listar
ls -la                        # Detallado + ocultos
ls -lh                        # Tamaños legibles
cd /ruta/absoluta             # Ir a ruta
cd ~                          # Home
cd -                          # Directorio anterior
cd ..                         # Subir nivel
```

## Archivos y Carpetas
```bash
mkdir carpeta                 # Crear carpeta
mkdir -p a/b/c                # Anidadas
touch archivo.txt             # Crear vacío
cp origen destino             # Copiar archivo
cp -r dir1 dir2               # Copiar carpeta
mv viejo nuevo                # Mover/renombrar
rm archivo                    # Borrar archivo
rm -r carpeta                 # Borrar carpeta
rm -rf carpeta                # Forzar (¡CUIDADO!)
```

## Ver Contenido
```bash
cat archivo                   # Todo
less archivo                  # Paginado (q=sale)
head -n 20 archivo            # Primeras 20 líneas
tail -n 20 archivo            # Últimas 20
tail -f archivo.log           # Seguir en vivo
wc -l archivo                 # Contar líneas
```

## Buscar
```bash
find . -name "*.js"           # Por nombre
find . -type f -name "*.py"   # Solo archivos
grep "texto" archivo          # En archivo
grep -r "texto" .             # Recursivo
grep -i "texto"               # Ignora mayúsculas
grep -n "texto"               # Con número línea
grep -v "texto"               # Líneas SIN texto
rg "patrón"                   # ripgrep (rápido)
fd "patrón"                   # find rápido
```

## Permisos
```bash
ls -l                         # Ver permisos
chmod +x script.sh            # Ejecutable
chmod 755 script.sh           # rwxr-xr-x
chmod 644 archivo.txt         # rw-r--r--
chown user:group archivo      # Cambiar dueño (sudo)
```

## Procesos
```bash
ps aux                        # Todos procesos
top                           # Interactivo
htop                          # Mejor (instalar)
kill PID                      # Matar suave
kill -9 PID                   # Forzar
pkill nombre                  # Por nombre
jobs                          # Background jobs
fg %1                         # Traer a foreground
bg %1                         # Reanudar background
nohup cmd &                   # Inmune a logout
```

## Redirección y Pipes
```bash
cmd > out.txt                 # Stdout a archivo (sobrescribe)
cmd >> out.txt                # Append
cmd 2> err.txt                # Stderr
cmd &> all.txt                # Todo
cmd1 | cmd2                   # Pipe
ls | grep ".txt"              # Filtrar
cat file | grep "x" | wc -l   # Cadena
```

## Historial y Atajos
```bash
history                       # Ver historial
!!                            # Último comando
!git                          # Último que empieza con git
Ctrl+R                        # Buscar historial (interactivo)
Ctrl+A                        # Inicio línea
Ctrl+E                        # Final línea
Ctrl+U                        # Borrar al inicio
Ctrl+K                        # Borrar al final
Ctrl+W                        # Borrar palabra
Ctrl+L                        # Limpiar pantalla
Ctrl+C                        # Cancelar
Ctrl+D                        # EOF (salir)
Tab                           # Autocompletar
Tab Tab                       # Mostrar opciones
```

## Variables y Entorno
```bash
echo $VAR                     # Ver variable
export VAR=valor              # Definir (sesión)
export PATH="$PATH:/nueva"    # Añadir a PATH
env                           # Todas variables
printenv VAR                  # Una variable
```

## Archivos Comprimidos
```bash
tar -czf arch.tar.gz dir/     # Crear .tar.gz
tar -xzf arch.tar.gz          # Extraer
tar -tzf arch.tar.gz          # Listar contenido
zip -r arch.zip dir/          # Crear .zip
unzip arch.zip                # Extraer
gzip archivo                  # Comprimir .gz
gunzip archivo.gz             # Descomprimir
```

## SSH
```bash
ssh user@host                 # Conectar
ssh -p 2222 user@host         # Puerto custom
ssh-keygen -t ed25519         # Generar llave
ssh-copy-id user@host         # Copiar llave pública
scp archivo user@host:/ruta   # Copiar a remoto
scp user@host:/ruta archivo   # Copiar de remoto
rsync -avz dir/ user@host:/ruta  # Sincronizar eficiente
```

## Red
```bash
curl url                      # GET
curl -X POST -d '{}' -H "Content-Type: application/json" url
curl -I url                   # Solo headers
wget url                      # Descargar
ping host                     # Conectividad
dig dominio                   # DNS
netstat -tulpn                # Puertos escuchando (sudo)
ss -tulpn                     # Moderno
```

## Disco y Sistema
```bash
df -h                         # Espacio disco
du -h --max-depth=1           # Tamaño carpetas
du -sh carpeta                # Tamaño total carpeta
free -h                       # Memoria
uptime                        # Tiempo encendido
whoami                        # Usuario actual
id                            # UID/GID
uname -a                      # Kernel info
```

## Herramientas Modernas (Instala)
```bash
# eza (ls mejor)
alias ls="eza --icons"
alias ll="eza -la --icons"
alias tree="eza --tree"

# bat (cat mejor)
alias cat="bat"

# fd (find mejor)
# rg (grep mejor)
# fzf (fuzzy finder - Ctrl+T archivos, Ctrl+R historial)
# htop/btop (top mejor)
# zoxide (cd inteligente)
# starship (prompt bonito)
```

## Termux (Android)
```bash
termux-setup-storage          # Acceso storage
termux-wake-lock              # Mantener CPU despierta
termux-wake-unlock            # Liberar
termux-notification -t "Tit" -c "Msg"
termux-tts-speak "Hola"
termux-battery-status
termux-wifi-scaninfo
pkg install git nodejs python ffmpeg neovim htop fzf ripgrep bat eza zoxide
```