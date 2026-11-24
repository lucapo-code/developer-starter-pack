# Clean Code: Código Legible y Mantenible

## 🎯 Principio Fundamental

> "El código se escribe una vez, se lee muchas veces. Escribe para que otros (y tú del futuro) lo entiendan."

---

## Nombrado: La Documentación Viva

```typescript
// ❌ Malo
const d = 86400;           // ¿qué es?
const u = getUser();       // ¿qué usuario?
const calc(x, y) { return x * y; }  // ¿qué calcula?

// ✅ Bueno
const SEGUNDOS_EN_UN_DIA = 86400;
const usuarioActual = obtenerUsuarioAutenticado();
function calcularAreaRectangulo(ancho: number, alto: number): number {
    return ancho * alto;
}

// Reglas:
// - Variables/funciones: camelCase, verbo + sustantivo (obtenerUsuario)
// - Clases/Interfaces: PascalCase (Usuario, CalculadoraArea)
// - Constantes: UPPER_SNAKE_CASE (MAX_INTENTOS)
// - Booleanos: prefijo is/has/can/should (isActivo, hasPermiso)
// - Evita: data, info, temp, val, obj, manager, handler, util
```

---

## Funciones: Pequeñas y Enfocadas

```typescript
// ❌ Malo: hace demasiado
async function procesarPedido(pedido: Pedido) {
    validar(pedido);
    const usuario = await db.getUsuario(pedido.usuarioId);
    const items = await db.getItems(pedido.itemsIds);
    const total = calcularTotal(items);
    await cobrar(usuario.tarjeta, total);
    await db.guardarPedido({ ...pedido, total });
    await email.enviarConfirmacion(usuario.email, pedido);
    await inventario.descontar(items);
}

// ✅ Bueno: una responsabilidad cada una
async function procesarPedido(pedido: Pedido): Promise<PedidoProcesado> {
    const pedidoValidado = validarPedido(pedido);
    const usuario = await obtenerUsuario(pedidoValidado.usuarioId);
    const items = await obtenerItems(pedidoValidado.itemsIds);
    const total = calcularTotal(items);
    const cobro = await procesarPago(usuario.metodoPago, total);
    const pedidoGuardado = await guardarPedido({ ...pedidoValidado, total, cobroId: cobro.id });
    await notificarConfirmacion(usuario.email, pedidoGuardado);
    await actualizarInventario(items);
    return pedidoGuardado;
}

// Reglas:
// - Máximo 20 líneas (ideal < 10)
// - Un nivel de indentación (máximo 2)
// - Sin efectos secundarios ocultos
// - Parámetros: 0-2 (3+ = objeto de opciones)
```

---

## Comentarios: El Código Se Explica Solo

```typescript
// ❌ Comentarios innecesarios
const edad = 25;           // Edad del usuario
const usuario = getUser(); // Obtiene usuario
if (edad > 18) {           // Si es mayor de edad
    // ... 
}

// ✅ Comentarios que valen la pena
// TODO: Migrar a nuevo proveedor de pagos (issue #123)
// FIXME: Race condition en alta concurrencia
// HACK: Workaround bug en lib v2.1 (upgrade cuando salga v2.2)
// WHY: Usamos bcrypt no argon2 por compatibilidad con legacy

// Regla: Si necesitas comentar QUÉ hace, renombra.
// Comenta solo POR QUÉ (contexto de negocio, tradeoffs, hacks).
```

---

## Estructura de Proyecto

```
src/
├── domain/           # Lógica de negocio pura (sin deps externas)
│   ├── entities/     # Usuario, Pedido, Producto
│   ├── value-objects/# Email, Dinero, Direccion
│   ├── services/     # CalcularDescuento, ValidarStock
│   └── events/       # PedidoCreado, PagoFallido
├── application/      # Casos de uso (orquesta domain)
│   ├── commands/     # CrearPedido, CancelarSuscripcion
│   ├── queries/      # ObtenerPedidosUsuario
│   └── dtos/         # Data Transfer Objects
├── infrastructure/   # Implementaciones técnicas
│   ├── database/     # Repositorios Prisma/SQLAlchemy
│   ├── http/         # Controllers, middlewares
│   ├── messaging/    # Colas, eventos
│   └── external/     # APIs terceros (Stripe, SendGrid)
├── interfaces/       # API REST, GraphQL, CLI
│   ├── routes/
│   ├── schemas/      # Zod, Pydantic
│   └── presenters/   # Formateo respuesta
└── shared/           # Utilidades genuinas
    ├── errors/       # AppError, ValidationError
    ├── guards/       # Auth, rate limit
    └── utils/        # date, string, array helpers
```

---

## Principios SOLID (Resumen Práctico)

| Principio | Qué Significa | Ejemplo |
|-----------|---------------|---------|
| **S**ingle Responsibility | Una clase = una razón para cambiar | `UsuarioService` no envía emails |
| **O**pen/Closed | Abierto a extensión, cerrado a modificación | `PagoStrategy` interface + `StripePago`, `PayPalPago` |
| **L**iskov Substitution | Subtipos sustituibles por su base | `Pato` y `PatoGoma` ambos `volar()`? No. Separar `Volador`. |
| **I**nterface Segregation | Interfaces pequeñas y específicas | `interface Imprimible { imprimir() }` no `interface TodoEnUno` |
| **D**ependency Inversion | Depende de abstracciones, no concreciones | `PedidoService` usa `RepositorioPedido` (interface), no `PrismaPedidoRepo` |

---

## Manejo de Errores

```typescript
// ❌ Malo: try/catch en todo lado, errores genéricos
try {
    const user = await db.getUser(id);
} catch (e) {
    console.error(e);
    return null;
}

// ✅ Bueno: Errores tipados, flujo explícito
class AppError extends Error {
    constructor(
        public readonly codigo: string,
        public readonly statusCode: number,
        public readonly detalles?: unknown
    ) {
        super(codigo);
        this.name = "AppError";
    }
}

class NoEncontradoError extends AppError {
    constructor(recurso: string, id: string) {
        super(`${recurso}_no_encontrado`, 404, { recurso, id });
    }
}

class ValidacionError extends AppError {
    constructor(campos: Record<string, string>) {
        super("validacion_fallida", 400, { campos });
    }
}

// En código: lanza, no atrapes (excepto boundary)
async function obtenerUsuario(id: string): Promise<Usuario> {
    const usuario = await repo.findById(id);
    if (!usuario) throw new NoEncontradoError("Usuario", id);
    return usuario;
}

// Boundary (controller/middleware): atrapa y formatea
app.use((err, req, res, next) => {
    if (err instanceof AppError) {
        return res.status(err.statusCode).json({ error: err.codigo, detalles: err.detalles });
    }
    logger.error(err);
    return res.status(500).json({ error: "interno" });
});
```

---

## Code Smells (Señales de Alerta)

| Smell | Síntoma | Refactor |
|-------|---------|----------|
| **Long Method** | >20 líneas | Extract Method |
| **Large Class** | >200 líneas, muchos métodos | Extract Class |
| **Primitive Obsession** | `string` para email, `number` para dinero | Value Objects |
| **Data Clumps** | Mismos 3-4 params juntos | Introduce Parameter Object |
| **Shotgun Surgery** | Cambio en 1 lugar = editar 10 archivos | Consolidar lógica |
| **Feature Envy** | Método usa más datos de otra clase | Move Method |
| **God Class** | Una clase lo sabe/hace todo | Split por responsabilidad |

---

## Formato Consistente (Automatiza)

```bash
# JavaScript/TypeScript
npm install -D prettier eslint @typescript-eslint/parser
# .prettierrc
{ "semi": true, "singleQuote": true, "tabWidth": 4, "printWidth": 100 }

# Python
pip install ruff black
# pyproject.toml
[tool.ruff] line-length = 100
[tool.black] line-length = 100

# Pre-commit (husky + lint-staged)
npx husky install
npx lint-staged  # Solo archivos staged
```

---

## Code Review Checklist

```
□ ¿Entiendo qué hace sin preguntar?
□ ¿Nombres claros y consistentes?
□ ¿Funciones pequeñas (<20 líneas)?
□ ¿Sin código comentado (basura)?
□ ¿Errores manejados explícitamente?
□ ¿Tests para lógica nueva?
□ ¿Sin console.log/print de debug?
□ ¿Types estrictos (no any)?
□ ¿Performance considerado (N+1, bucles)?
□ ¿Seguridad (validación, sanitización)?
□ ¿Documentación actualizada (README, ADR)?
```

---

## Refactoring Seguro

1. **Tests verdes** → Refactor → Tests verdes
2. **Pequeños pasos** (Extract Method, Rename, Move)
3. **Commit por refactor** (separado de features)
4. **Herramientas**: VS Code refactor, `ts-morph`, `coc.nvim`

---

## Cheatsheet Clean Code

```
┌────────────────────────────────────────────────────────────────┐
│ NOMBRADO             FUNCIONES              ERRORES            │
│ verbo+sustantivo       < 20 líneas           Tipados (clases)   │
│ is/has/can booleanos   0-2 params             Boundary atrapa   │
│ PascalCase clases      1 nivel indent         Lanza temprano    │
│ UPPER_CONSTANTES       Sin side effects       Contexto en error │
├────────────────────────────────────────────────────────────────┤
│ COMENTARIOS            ESTRUCTURA             REVIEW            │
│ Solo POR QUÉ           Domain/App/Infra       Tests verdes      │
│ TODO/FIXME/HACK/WHY    Separación clara       Nombres claros    │
│ No qué (código)        Interfaces en domain   Sin any/console   │
├────────────────────────────────────────────────────────────────┤
│ SOLID                  SMELLS                 TOOLS             │
│ S: 1 responsabilidad   Long Method            Prettier/Ruff     │
│ O: Open/Closed         Primitive Obsession   ESLint/Mypy       │
│ L: Liskov              Data Clumps            Husky + lint-staged│
│ I: Interface segreg.   God Class             Git bisect        │
│ D: Dependency inv.     Feature Envy          Refactor tools    │
└────────────────────────────────────────────────────────────────┘
```

---

## Ejercicios

1. **Renombra**: Toma archivo legacy, renombra todo a intención reveladora
2. **Extrae**: Función de 50 líneas → 5 funciones de 10
3. **Value Object**: Reemplaza `string email` por `class Email { constructor(v) { validar(v); this.valor = v; } }`
4. **Error Tipado**: Reemplaza `throw new Error()` por jerarquía `AppError`
5. **Review**: Haz PR a repo propio, revisa con checklist

---

## Recursos

- [Clean Code (Libro)](https://www.oreilly.com/library/view/clean-code/9780136083238/) - Robert Martin
- [Refactoring (Libro)](https://martinfowler.com/books/refactoring.html) - Martin Fowler
- [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Refactoring Guru](https://refactoring.guru/) - Patrones y code smells visual
- [TypeScript Best Practices](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet)
- [Google TypeScript Style](https://google.github.io/styleguide/tsguide.html)

---

## Siguiente Paso

→ [Documentación](documentation.md) - README, API docs, ADRs
→ [CI/CD](cicd.md) - Automatiza calidad
→ [Arquitectura](architecture.md) - Patrones de alto nivel