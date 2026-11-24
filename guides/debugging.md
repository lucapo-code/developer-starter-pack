# Debugging: Encontrar y Arreglar Bugs

## 🎯 Mentalidad de Debugging

> "El debugging es el doble de difícil que escribir el código. Si escribes el código tan inteligentemente como puedes, por definición no eres lo suficientemente inteligente para depurarlo." — Brian Kernighan

### Proceso Científico
1. **Reproduce** - Haz que falle consistentemente
2. **Aísla** - Reduce al mínimo código que falla
3. **Hipótesis** - ¿Por qué falla?
4. **Experimenta** - Prueba tu hipótesis
5. **Arregla** - Corrige la causa raíz
6. **Verifica** - Test pasa, no regresiones
7. **Previene** - Test unitario + lint rule

---

## Herramientas por Entorno

### Navegador (Frontend)
```javascript
// Console
console.log("valor:", variable);          // Básico
console.table(arrayObjetos);              // Tabla bonita
console.dir(objeto);                      // Props expandibles
console.trace();                          // Stack trace
console.time("label"); console.timeEnd("label"); // Timing

// Debugger (pausa ejecución)
debugger;  // En código - abre DevTools

// DevTools Sources Panel
// - Breakpoints (click línea)
// - Conditional breakpoints (right click → Edit breakpoint)
// - Logpoints (log sin pausar)
// - Watch expressions
// - Call stack
// - Scope variables
// - Async stack traces (await)

// Network tab
// - Filter: Fetch/XHR, WS, Img
// - Headers, Payload, Response, Timing
// - Copy as curl/fetch
// - Block request URL (test fallback)

// React/Vue DevTools
// - Component tree + props/state
// - Highlight updates
// - Profiler (renders lentos)
```

### Node.js / Backend
```bash
# Inspector nativo
node --inspect app.js              # Puerto 9229
node --inspect-brk app.js          # Pausa en primera línea

# Con VS Code (launch.json)
{
    "type": "node",
    "request": "launch",
    "name": "Debug App",
    "program": "${workspaceFolder}/app.js",
    "console": "integratedTerminal"
}

# Console avanzado
console.log(util.inspect(obj, { depth: null, colors: true }));
```

### Python
```python
# Print debugging
print(f"variable={variable!r}")  # !r = repr

# Icecream (mejor print)
pip install icecream
from icecream import ic
ic(variable)  # ic| variable: 42

# Debugger nativo
import pdb; pdb.set_trace()  # Pausa
# n=next, s=step, c=continue, p=print, l=list, q=quit

# Mejor: ipdb
pip install ipdb
import ipdb; ipdb.set_trace()

# VS Code launch.json
{
    "type": "python",
    "request": "launch",
    "name": "Debug",
    "program": "${file}",
    "console": "integratedTerminal"
}
```

### VS Code Debugging (Universal)
```json
// .vscode/launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug Node",
            "type": "node",
            "request": "launch",
            "program": "${workspaceFolder}/src/index.js",
            "console": "integratedTerminal"
        },
        {
            "name": "Debug Python",
            "type": "python",
            "request": "launch",
            "program": "${file}"
        },
        {
            "name": "Attach to Chrome",
            "type": "chrome",
            "request": "attach",
            "port": 9222,
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

---

## Técnicas Universales

### 1. Binary Search (Divide y Vencerás)
```javascript
// Código que falla en línea 500
// Comenta mitad → funciona? → falla en la otra mitad
// Repite hasta aislar 1-5 líneas
```

### 2. Git Bisect (¿Cuándo se rompió?)
```bash
git bisect start
git bisect bad          # HEAD actual falla
git bisect good v1.0.0  # Esta versión funciona
# Git hace checkout a commit medio
# Pruebas → git bisect good/bad
# Repite hasta encontrar commit culpable
git bisect reset
```

### 3. Logging Estratégico
```javascript
// Niveles
logger.debug("Detalle: ", { userId, action });    // Desarrollo
logger.info("Usuario logueado", { userId });       // Producción
logger.warn("Reintento 3/5", { error });           // Atención
logger.error("Pago falló", { userId, error });     // Alerta

// Correlation ID (trazar request)
const requestId = crypto.randomUUID();
logger.info("Request", { requestId, method, url });
// Propaga en headers: x-request-id
```

### 4. Rubber Duck Debugging
> Explica el código línea por línea a un pato de goma (o colega).
> El acto de verbalizar revela el bug el 50% de las veces.

---

## Patrones de Bugs Comunes

| Síntoma | Causa Probable | Solución |
|---------|----------------|----------|
| `undefined is not a function` | Import mal, typo, versión | Check imports, types |
| `Cannot read property of null` | Optional chaining faltante | `obj?.prop` |
| Race condition | Async sin await, estado compartido | Mutex, queue, atomic ops |
| Memory leak | Event listeners, closures, caches | Cleanup, WeakMap, límites |
| Off-by-one | `<=` vs `<`, `length-1` | Test edges: 0, 1, max |
| Timezone bug | `Date` sin zona, UTC vs local | `date-fns-tz`, `luxon`, UTC siempre |
| Float precision | `0.1 + 0.2 = 0.30000000000000004` | `decimal.js`, centavos enteros |
| Caché stale | Cache-Control, SW, CDN | Versiona assets, cache busting |

---

## Debugging en Producción (Sin Romper)

```javascript
// Feature flags para debugging seguro
if (process.env.DEBUG_USER === userId) {
    logger.debug("Debug user", { data: sensitiveData });
}

// Non-breaking breakpoints (Logpoints)
// VS Code: right click línea → Add Logpoint
// "Valor de x: {x}, y: {y}"

// Sampling (1% de requests)
if (Math.random() < 0.01) {
    logger.debug("Sample", { fullState });
}

// Sentry / Datadog / Honeycomb
// - Error tracking automático
// - Breadcrumbs (qué pasó antes)
// - Release tracking
// - Alertas
```

---

## Checklist de Debugging

```
□ ¿Puedo reproducir consistentemente?
□ ¿Cuál es el comportamiento esperado vs actual?
□ ¿Qué cambió recientemente? (git log, deps, config)
□ ¿Hay logs/errores en consola/servidor?
□ ¿El bug es determinístico o aleatorio?
□ ¿Afecta a todos o solo algunos usuarios?
□ ¿He probado en entorno limpio (incógnito, fresh clone)?
□ ¿He aislado el código mínimo que falla?
□ ¿Mi fix rompe algo más? (corre tests)
□ ¿He agregado test de regresión?
```

---

## Ejercicios Prácticos

1. **Console**: Abre DevTools en cualquier web, usa `console.table` en array de objetos
2. **Breakpoint**: Pausa en `fetch`, inspecciona `response.json()`
3. **Git bisect**: Rompe algo a propósito, usa bisect para encontrarlo
4. **Rubber duck**: Explica en voz alta tu último bug arreglado
5. **Logpoint**: Agrega logpoint en VS Code sin parar ejecución

---

## Recursos

- [Chrome DevTools](https://developer.chrome.com/docs/devtools/) - Docs oficiales
- [Debugging Node.js](https://nodejs.org/en/docs/guides/debugging-getting-started/)
- [Python Debugging](https://realpython.com/python-debugging-pdb/)
- [VS Code Debugging](https://code.visualstudio.com/docs/editor/debugging)
- [Julia Evans Debugging](https://jvns.ca/blog/debugging-zine/) - Zine visual
- [Effective Debugging](https://www.oreilly.com/library/view/effective-debugging/9780134284860/) - Libro
- [Your Code as a Crime Scene](https://www.adamtornhill.com/articles/crimescene/crimescene.htm) - Análisis forense

---

## Siguiente Paso

→ [Clean Code](clean-code.md) - Código que no necesita debugging
→ [Documentación](documentation.md) - README, API docs, ADRs
→ [CI/CD](cicd.md) - Automatiza calidad