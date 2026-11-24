# Python Básico: Versátil, Legible, Potente

## 🎯 Por Qué Python

- **Sintaxis limpia**: Pseudocódigo ejecutable
- **Todo terreno**: Web, datos, IA, scripting, automatización
- **Ecosistema enorme**: PyPI (400k+ paquetes)
- **Comunidad gigante**: Respuestas en StackOverflow, tutoriales

---

## Instalación y Entorno

```bash
# Ver versión
python3 --version
# Python 3.11+

# Gestor de paquetes
pip install paquete

# Entorno virtual (¡SIEMPRE úsalo!)
python3 -m venv venv
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate     # Windows

# Dependencias del proyecto
pip freeze > requirements.txt
pip install -r requirements.txt
```

---

## Sintaxis Básica

```python
# Variables (tipado dinámico)
nombre = "Lucas"
edad = 25
altura = 1.75
es_dev = True
nada = None

# Comentarios
# Una línea
"""
Docstring: documentación de módulo/función/clase
"""

# Indentación = bloques (OBLIGATORIO, 4 espacios)
if edad >= 18:
    print("Mayor de edad")
else:
    print("Menor de edad")
```

---

## Estructuras de Datos

```python
# Listas (mutables, ordenadas)
frutas = ["manzana", "banana", "cereza"]
frutas.append("durazno")
frutas[0] = "kiwi"
frutas[1:3]  # ["banana", "cereza"] (slice)

# Tuplas (inmutables)
coordenadas = (10, 20)
x, y = coordenadas  # Desempaquetado

# Sets (únicos, no ordenados)
unicos = {1, 2, 2, 3}  # {1, 2, 3}
unicos.add(4)

# Diccionarios (clave-valor)
usuario = {"nombre": "Lucas", "edad": 25}
usuario["email"] = "lucas@mail.com"
usuario.get("telefono", "No tiene")  # Default si no existe

# Comprensiones (pythonic)
cuadrados = [x**2 for x in range(10)]
pares = {x for x in range(10) if x % 2 == 0}
dobles = {x: x*2 for x in range(5)}
```

---

## Control de Flujo

```python
# If/elif/else
if edad < 18:
    categoria = "menor"
elif edad < 65:
    categoria = "adulto"
else:
    categoria = "jubilado"

# Expresión ternaria
estado = "activo" if es_dev else "inactivo"

# Match/Case (Python 3.10+)
match comando:
    case "iniciar":
        print("Iniciando...")
    case "detener":
        print("Deteniendo...")
    case _:
        print("Comando desconocido")

# Bucles
for fruta in frutas:
    print(fruta)

for i, fruta in enumerate(frutas):
    print(f"{i}: {fruta}")

for clave, valor in usuario.items():
    print(f"{clave}: {valor}")

# While
while contador > 0:
    contador -= 1

# Break/Continue/Else
for n in range(10):
    if n == 3:
        continue  # Salta 3
    if n == 7:
        break     # Para en 7
    print(n)
else:
    print("Bucle completado sin break")
```

---

## Funciones

```python
# Definición
def saludar(nombre: str, saludo: str = "Hola") -> str:
    """Retorna saludo personalizado."""
    return f"{saludo}, {nombre}!"

# Type hints (opcionales pero recomendados)
def sumar(a: int, b: int) -> int:
    return a + b

# Args y kwargs
def flexible(*args, **kwargs):
    print(args)    # Tupla posicionales
    print(kwargs)  # Dict nombrados

flexible(1, 2, a=3, b=4)

# Lambda (función anónima simple)
doble = lambda x: x * 2
lista = [1, 2, 3]
list(map(doble, lista))  # [2, 4, 6]

# Decoradores
def timer(func):
    import time
    def wrapper(*args, **kwargs):
        inicio = time.time()
        resultado = func(*args, **kwargs)
        print(f"{func.__name__} tardó {time.time()-inicio:.3f}s")
        return resultado
    return wrapper

@timer
def lenta():
    time.sleep(1)
```

---

## Clases y POO

```python
from dataclasses import dataclass
from typing import Optional
from datetime import datetime

# Dataclass (Python 3.7+): menos boilerplate
@dataclass
class Usuario:
    nombre: str
    email: str
    edad: int = 18
    activo: bool = True
    creado: datetime = None  # type: ignore

    def __post_init__(self):
        if self.creado is None:
            self.creado = datetime.now()

    def saludar(self) -> str:
        return f"Hola, soy {self.nombre}"

# Herencia
@dataclass
class Admin(Usuario):
    permisos: list[str] = None  # type: ignore

    def __post_init__(self):
        super().__post_init__()
        if self.permisos is None:
            self.permisos = ["leer", "escribir", "borrar"]

# Uso
user = Usuario("Lucas", "lucas@mail.com", 25)
admin = Admin("Ana", "ana@mail.com", 30, permisos=["todo"])
```

---

## Manejo de Archivos

```python
# Leer todo
with open("archivo.txt", "r", encoding="utf-8") as f:
    contenido = f.read()

# Leer líneas
with open("archivo.txt", "r") as f:
    lineas = f.readlines()
    # o iterar:
    for linea in f:
        print(linea.strip())

# Escribir
with open("salida.txt", "w", encoding="utf-8") as f:
    f.write("Hola\n")
    f.writelines(["Línea 1\n", "Línea 2\n"])

# JSON
import json
with open("data.json", "w") as f:
    json.dump({"usuarios": ["A", "B"]}, f, indent=2, ensure_ascii=False)

with open("data.json", "r") as f:
    data = json.load(f)

# Pathlib (moderno, orientado a objetos)
from pathlib import Path
ruta = Path("carpeta/archivo.txt")
ruta.parent.mkdir(parents=True, exist_ok=True)
ruta.write_text("Contenido")
texto = ruta.read_text()
```

---

## Manejo de Errores

```python
# Excepciones básicas
try:
    resultado = 10 / 0
except ZeroDivisionError:
    print("No se divide por cero")
except ValueError as e:
    print(f"Valor inválido: {e}")
except Exception as e:
    print(f"Error inesperado: {e}")
else:
    print("Sin errores")
finally:
    print("Limpieza siempre")

# Raise
def dividir(a: float, b: float) -> float:
    if b == 0:
        raise ValueError("Divisor no puede ser cero")
    return a / b

# Context manager (with)
class Conexion:
    def __enter__(self):
        print("Conectando...")
        return self
    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Desconectando...")
        return False  # No suprimir excepciones

with Conexion() as db:
    db.query("SELECT * FROM users")
```

---

## Paquetería Esencial

```bash
# Web
pip install fastapi uvicorn      # API moderna, rápida
pip install flask                # Microframework clásico
pip install django               # Framework completo (batteries included)
pip install requests             # HTTP cliente simple
pip install httpx                # HTTP cliente async (moderno)

# Datos
pip install pandas               # DataFrames, análisis
pip install numpy                # Arrays numéricos
pip install sqlalchemy           # ORM
pip install pydantic             # Validación de datos (tipos)

# Testing
pip install pytest               # Testing moderno
pip install pytest-cov           # Cobertura

# Calidad
pip install ruff                 # Linter/formatter rápido (Rust)
pip install black                # Formatter opinión
pip install mypy                 # Type checker

# Utilidades
pip install python-dotenv        # Cargar .env
pip install rich                 # Terminal bonita
pip install typer                # CLI apps fácil
pip install loguru               # Logging mejor
```

---

## Proyecto Mínimo: API con FastAPI

```python
# main.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional
from datetime import datetime

app = FastAPI(title="Mi API")

class Tarea(BaseModel):
    titulo: str
    descripcion: Optional[str] = None
    completada: bool = False

tareas_db: dict[int, Tarea] = {}
contador = 0

@app.get("/")
def root():
    return {"mensaje": "API de Tareas", "docs": "/docs"}

@app.get("/tareas")
def listar_tareas():
    return list(tareas_db.values())

@app.get("/tareas/{tarea_id}")
def obtener_tarea(tarea_id: int):
    if tarea_id not in tareas_db:
        raise HTTPException(404, "Tarea no encontrada")
    return tareas_db[tarea_id]

@app.post("/tareas")
def crear_tarea(tarea: Tarea):
    global contador
    contador += 1
    tareas_db[contador] = tarea
    return {"id": contador, **tarea.model_dump()}

@app.put("/tareas/{tarea_id}")
def actualizar_tarea(tarea_id: int, tarea: Tarea):
    if tarea_id not in tareas_db:
        raise HTTPException(404, "Tarea no encontrada")
    tareas_db[tarea_id] = tarea
    return tareas_db[tarea_id]

@app.delete("/tareas/{tarea_id}")
def borrar_tarea(tarea_id: int):
    if tarea_id not in tareas_db:
        raise HTTPException(404, "Tarea no encontrada")
    del tareas_db[tarea_id]
    return {"ok": True}

# Ejecutar: uvicorn main:app --reload
# Docs: http://localhost:8000/docs
```

---

## Testing con pytest

```python
# test_tareas.py
import pytest
from main import app
from fastapi.testclient import TestClient

client = TestClient(app)

def test_root():
    response = client.get("/")
    assert response.status_code == 200
    assert "mensaje" in response.json()

def test_crear_tarea():
    response = client.post("/tareas", json={"titulo": "Test"})
    assert response.status_code == 200
    data = response.json()
    assert data["titulo"] == "Test"
    assert "id" in data

def test_404():
    response = client.get("/tareas/999")
    assert response.status_code == 404
```

```bash
pytest -v              # Verboso
pytest --cov=main      # Con cobertura
pytest -k "test_crear" # Solo tests que matcheen
```

---

## Cheatsheet Python

```
┌────────────────────────────────────────────────────────────────┐
│ BÁSICO               ESTRUCTURAS            FUNCIONES          │
│ print/f-string       list/dict/set          def/lambda         │
│ if/elif/else         comprehensions         *args/**kwargs     │
│ for/while            enumerate/zip          type hints         │
│ try/except/finally   slicing                decorators         │
├────────────────────────────────────────────────────────────────┤
│ CLASES               ARCHIVOS               PAQUETES           │
│ @dataclass           with open()            pip install        │
│ herencia             pathlib.Path           requirements.txt   │
│ type hints           json.load/dump         venv               │
├────────────────────────────────────────────────────────────────┤
│ WEB                  DATOS                  CALIDAD            │
│ fastapi/flask        pandas/numpy           pytest             │
│ pydantic             sqlalchemy             ruff/black/mypy    │
│ httpx/requests       matplotlib             python-dotenv      │
└────────────────────────────────────────────────────────────────┘
```

---

## Ejercicios Progresivos

1. **Hola Mundo**: Script que salude por nombre (argumento CLI)
2. **Calculadora**: Funciones `sumar`, `restar`, `multiplicar`, `dividir` con manejo de errores
3. **Archivos**: Lee `nombres.txt` (uno por línea), escribe `saludos.txt` con "Hola {nombre}"
4. **JSON**: Carga `usuarios.json`, filtra mayores de 18, guarda `adultos.json`
5. **API**: FastAPI con CRUD de "notas" (titulo, contenido, tags)
6. **Test**: Escribe tests para la API anterior
7. **CLI**: Usa `typer` para hacer CLI de la calculadora

---

## Recursos

- [Python.org Tutorial](https://docs.python.org/3/tutorial/) - Oficial
- [Real Python](https://realpython.com/) - Artículos profundos
- [Python Cheatsheet](https://www.pythoncheatsheet.org/) - Referencia rápida
- [Effective Python](https://effectivepython.com/) - 90 mejores prácticas
- [FastAPI Docs](https://fastapi.tiangolo.com/) - Mejor tutorial API
- [Pytest Docs](https://docs.pytest.org/) - Testing
- [Corey Schafer](https://www.youtube.com/@coreyms) - YouTube excelente

---

## Siguiente Paso

→ [Bases de Datos](databases.md) - SQL, NoSQL, ORMs
→ [Testing](testing.md) - Unit, integration, E2E
→ [Clean Code](clean-code.md) - Código mantenible