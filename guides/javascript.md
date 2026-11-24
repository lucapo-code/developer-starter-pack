# JavaScript Moderno (ES6+): La Lengua de la Web

## 🎯 Objetivo
Dominar JavaScript moderno para crear aplicaciones web interactivas.

---

## Dónde Corre JavaScript

```html
<!-- En el navegador -->
<script src="app.js"></script>
<script type="module" src="app.js"></script>  <!-- Módulos ES6 -->

<!-- En Node.js (backend, herramientas) -->
node app.js
```

---

## Variables: `const`, `let`, NUNCA `var`

```javascript
// const = referencia inmutable (preferido por defecto)
const PI = 3.14159;
const usuario = { nombre: "Lucas" };
usuario.nombre = "Ana";  // ✅ OK: mutas el objeto
// usuario = {};         // ❌ Error: reasignas la variable

// let = reasignable
let contador = 0;
contador = 1;  // ✅ OK

// var = NO USAR (scope function, hoisting confuso)
// var nombre = "viejo";
```

---

## Tipos de Datos

```javascript
// Primitivos (inmutables)
const nombre = "Lucas";      // string
const edad = 25;             // number
const activo = true;         // boolean
const nada = null;           // null (intencional)
const indefinido;            // undefined (no asignado)
const id = Symbol("id");     // symbol (único)
const grande = 9007199254740991n; // bigint

// Objetos (mutables, referencia)
const persona = { nombre: "Lucas", edad: 25 };
const colores = ["rojo", "verde", "azul"];  // Array = objeto
const fecha = new Date();                   // Objeto Date
const regex = /patron/;                     // RegExp
```

---

## Funciones Modernas

```javascript
// Declaración (hoisted)
function saludar(nombre) {
    return `Hola, ${nombre}!`;
}

// Expresión (no hoisted)
const saludar = function(nombre) {
    return `Hola, ${nombre}!`;
};

// Arrow function (concisa, this léxico)
const saludar = (nombre) => `Hola, ${nombre}!`;

// Parámetros por defecto
const saludar = (nombre = "Invitado") => `Hola, ${nombre}!`;

// Rest parameters
const sumar = (...nums) => nums.reduce((a, b) => a + b, 0);
sumar(1, 2, 3);  // 6

// Destructuring en parámetros
const crearUsuario = ({ nombre, edad = 18, pais = "AR" }) => ({
    nombre, edad, pais, id: Date.now()
});
```

---

## Objetos y Arrays: Métodos Esenciales

```javascript
// Objetos
const user = { nombre: "Lucas", edad: 25, activo: true };

Object.keys(user);      // ["nombre", "edad", "activo"]
Object.values(user);    // ["Lucas", 25, true]
Object.entries(user);   // [["nombre","Lucas"], ["edad",25], ...]
{ ...user, edad: 26 }   // Spread: copia + sobrescribe

// Arrays
const nums = [1, 2, 3, 4, 5];

// Transformación (inmutables - devuelven nuevo array)
nums.map(n => n * 2);           // [2, 4, 6, 8, 10]
nums.filter(n => n > 2);        // [3, 4, 5]
nums.find(n => n > 2);          // 3 (primer match)
nums.reduce((acc, n) => acc + n, 0);  // 15 (acumulador)
nums.flatMap(n => [n, n*2]);    // [1, 2, 2, 4, 3, 6, ...]

// Iteración (side effects)
nums.forEach(n => console.log(n));

// Comprobación
nums.some(n => n > 3);   // true (al menos uno)
nums.every(n => n > 0);  // true (todos)

// Ordenación (MUTA el original)
nums.sort((a, b) => a - b);  // Ascendente numérico
```

---

## Destructuring (Desestructuración)

```javascript
// Arrays
const [primero, segundo, ...resto] = [1, 2, 3, 4, 5];
// primero=1, segundo=2, resto=[3,4,5]

// Objetos
const { nombre, edad, pais = "AR" } = { nombre: "Lucas", edad: 25 };
// nombre="Lucas", edad=25, pais="AR" (default)

// Renombrar
const { nombre: userName } = { nombre: "Lucas" };

// Anidado
const { direccion: { ciudad } } = { direccion: { ciudad: "Buenos Aires" } };
```

---

## Template Literals (Strings Modernos)

```javascript
const nombre = "Lucas";
const edad = 25;

// Interpolación
`Hola, ${nombre}! Tienes ${edad} años.`;

// Multilínea
const html = `
    <div class="card">
        <h2>${nombre}</h2>
        <p>Edad: ${edad}</p>
    </div>
`;

// Tagged templates (avanzado)
function upper(strings, ...values) {
    return strings.reduce((acc, str, i) => 
        acc + str + (values[i]?.toUpperCase() || "")
    , "");
}
upper`Hola ${nombre}`;  // "Hola LUCAS"
```

---

## Async/Await: Promesas Legibles

```javascript
// Promesa básica
const esperar = (ms) => new Promise(resolve => setTimeout(resolve, ms));

// Async/await (secuencial, legible)
async function main() {
    console.log("Inicio");
    await esperar(1000);
    console.log("Después de 1s");
    await esperar(1000);
    console.log("Después de 2s");
}

// Paralelo (más rápido)
async function paralelo() {
    const [users, posts] = await Promise.all([
        fetch("/api/users").then(r => r.json()),
        fetch("/api/posts").then(r => r.json())
    ]);
    return { users, posts };
}

// Manejo de errores
async function seguro() {
    try {
        const data = await fetch("/api/datos").then(r => r.json());
        return data;
    } catch (error) {
        console.error("Falló:", error.message);
        return null;  // o re-lanzar: throw error
    } finally {
        console.log("Limpieza");
    }
}

// Top-level await (en módulos)
const data = await fetch("/api/data").then(r => r.json());
```

---

## Módulos ES6 (Import/Export)

```javascript
// math.js
export const PI = 3.14159;
export function sumar(a, b) { return a + b; }
export function restar(a, b) { return a - b; }
export default class Calculadora { ... }

// app.js
import { PI, sumar } from "./math.js";           // Named imports
import Calculadora from "./math.js";             // Default import
import * as Math from "./math.js";               // Namespace
import { sumar as add } from "./math.js";        // Alias

// Dynamic import (lazy loading)
const { sumar } = await import("./math.js");
```

---

## Clases (Syntax Sugar)

```javascript
class Usuario {
    #privado = "secreto";  // Campo privado (ES2022)

    constructor(nombre, email) {
        this.nombre = nombre;
        this.email = email;
        this.creado = new Date();
    }

    // Getter
    get info() {
        return `${this.nombre} <${this.email}>`;
    }

    // Setter
    set nombre(valor) {
        this._nombre = valor.trim().toUpperCase();
    }

    // Método
    saludar() {
        return `Hola, soy ${this.nombre}`;
    }

    // Método estático
    static fromJSON(json) {
        return new Usuario(json.nombre, json.email);
    }
}

// Herencia
class Admin extends Usuario {
    constructor(nombre, email, permisos) {
        super(nombre, email);
        this.permisos = permisos;
    }

    // Sobrescribe
    saludar() {
        return `${super.saludar()} (Admin)`;
    }
}
```

---

## Manejo de Errores

```javascript
// Error personalizado
class ValidationError extends Error {
    constructor(campo, mensaje) {
        super(mensaje);
        this.name = "ValidationError";
        this.campo = campo;
    }
}

// Try/catch/finally
try {
    const usuario = await obtenerUsuario(id);
    if (!usuario) throw new ValidationError("id", "Usuario no encontrado");
    validar(usuario);
} catch (error) {
    if (error instanceof ValidationError) {
        return { error: error.campo, mensaje: error.message };
    }
    // Error inesperado: log y re-lanzar
    console.error(error);
    throw error;
} finally {
    cerrarConexion();
}
```

---

## Patrones Útiles

```javascript
// Optional chaining (?. - evita "cannot read property of undefined")
const ciudad = usuario?.direccion?.ciudad?.toUpperCase();

// Nullish coalescing (?? - solo null/undefined, no falsy)
const nombre = usuario?.nombre ?? "Anónimo";

// Default params + destructuring
function conectar({ host = "localhost", puerto = 3000, ssl = false } = {}) {
    // ...
}

// Object.fromEntries (array → objeto)
const params = new URLSearchParams("?a=1&b=2");
const obj = Object.fromEntries(params);  // {a: "1", b: "2"}

// Array.from (iterable → array)
const nodos = Array.from(document.querySelectorAll("div"));
const rango = Array.from({ length: 5 }, (_, i) => i * 2);  // [0,2,4,6,8]

// Flat (aplanar arrays anidados)
[[1,2], [3,4]].flat();           // [1,2,3,4]
[1, [2, [3]]].flat(2);           // [1,2,3]

// Agrupar por propiedad
const byRole = usuarios.reduce((acc, u) => {
    (acc[u.rol] ||= []).push(u);
    return acc;
}, {});
```

---

## Fetch API (HTTP Nativo)

```javascript
// GET
const usuarios = await fetch("/api/usuarios")
    .then(r => { if (!r.ok) throw new Error(r.statusText); return r.json(); });

// POST
const nuevo = await fetch("/api/usuarios", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ nombre: "Lucas", email: "lucas@mail.com" })
}).then(r => r.json());

// Con AbortController (timeout/cancelación)
const controller = new AbortController();
const timeout = setTimeout(() => controller.abort(), 5000);

try {
    const data = await fetch("/api/lento", { signal: controller.signal })
        .then(r => r.json());
} catch (e) {
    if (e.name === "AbortError") console.log("Timeout!");
} finally {
    clearTimeout(timeout);
}
```

---

## LocalStorage / SessionStorage

```javascript
// Guardar (solo strings)
localStorage.setItem("tema", "oscuro");
localStorage.setItem("usuario", JSON.stringify({ nombre: "Lucas" }));

// Leer
const tema = localStorage.getItem("tema");
const usuario = JSON.parse(localStorage.getItem("usuario") || "null");

// Borrar
localStorage.removeItem("tema");
localStorage.clear();  // Todo

// SessionStorage = se borra al cerrar pestaña
sessionStorage.setItem("token", "abc123");
```

---

## Cheatsheet JS Moderno

```
┌────────────────────────────────────────────────────────────────┐
│ VARIABLES          FUNCIONES            OBJETOS/ARRAYS         │
│ const / let        arrow =>             map/filter/reduce      │
│ NUNCA var          default params       find/some/every        │
│                    rest ...             spread {...}           │
│                    destructuring        Object.keys/values     │
├────────────────────────────────────────────────────────────────┤
│ ASYNC              MÓDULOS              ERRORES                │
│ async/await        import/export        try/catch/finally      │
│ Promise.all()      default/named        throw new Error()      │
│ Promise.race()     dynamic import()     custom Error class     │
│ fetch()            type="module"        optional chaining ?.   │
├────────────────────────────────────────────────────────────────┤
│ STRINGS            CLASES               UTILIDADES             │
│ template \`${}\`    class/extends       ?? nullish coalesce    │
│ tagged templates   #private fields      Array.from/flat        │
│                    static/get/set       Object.fromEntries     │
└────────────────────────────────────────────────────────────────┘
```

---

## Ejercicios Progresivos

### Nivel 1: Básico
1. Crea función `saludar(nombre)` con arrow function
2. Usa `map` para duplicar array `[1,2,3]` → `[2,4,6]`
3. Destructuring: extrae `nombre` y `edad` de `{nombre: "A", edad: 20, ciudad: "B"}`

### Nivel 2: Intermedio
4. `fetch` a `https://jsonplaceholder.typicode.com/users` y muestra nombres
5. Crea clase `Tarea` con `titulo`, `completada`, `toggle()`
6. Guarda/lee array de tareas en `localStorage`

### Nivel 3: Avanzado
7. Implementa `debounce(fn, ms)` que retrasa ejecución
8. Crea hook personalizado `useFetch(url)` (concepto React)
9. Maneja errores con `try/catch` y `AbortController` para timeout

---

## Recursos

- [MDN JavaScript](https://developer.mozilla.org/es/docs/Web/JavaScript) - Referencia oficial
- [JavaScript.info](https://javascript.info/) - Tutorial moderno excelente
- [ES6 Features](http://es6-features.org/) - Resumen visual ES6+
- [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS) - Profundo (gratis)
- [Modern JS Cheatsheet](https://github.com/mbeaudru/modern-js-cheatsheet) - Referencia rápida
- [Midudev JS](https://midu.dev/javascript/) - En español

---

## Siguiente Paso

→ [Python Básico](python.md) - Backend, datos, scripting
→ [Bases de Datos](databases.md) - SQL, NoSQL
→ [Node.js](nodejs.md) - JS en el servidor