# Testing: Confianza en Tu Código

## 🎯 Por Qué Testear

- **Detecta bugs temprano** (antes de producción)
- **Documenta comportamiento** esperado
- **Permite refactorizar sin miedo**
- **CI/CD confiable** (deploy automático)

---

## Pirámide de Testing

```
        /\
       /  \      E2E (Pocos, lentos, caros)
      /----\     - Cypress, Playwright
     /      \
    /--------\   Integración (Medios)
   /          \  - API, DB, servicios externos
  /------------\ Unitarios (Muchos, rápidos, baratos)
 /              \ - Funciones puras, lógica de negocio
```

**Regla**: 70% Unit / 20% Integración / 10% E2E

---

## Testing en JavaScript/TypeScript (Vitest + Testing Library)

```bash
npm install -D vitest @testing-library/react @testing-library/user-event jsdom happy-dom
```

```typescript
// vitest.config.ts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";

export default defineConfig({
    plugins: [react()],
    test: {
        environment: "happy-dom",
        globals: true,
        setupFiles: ["./test/setup.ts"],
        coverage: { provider: "v8", reporter: ["text", "html"] },
    },
});
```

### Unit Tests (Lógica Pura)

```typescript
// utils/math.ts
export const sumar = (a: number, b: number) => a + b;
export const dividir = (a: number, b: number) => {
    if (b === 0) throw new Error("Divisor cero");
    return a / b;
};

// utils/math.test.ts
import { describe, it, expect, expectTypeOf } from "vitest";
import { sumar, dividir } from "./math";

describe("math", () => {
    describe("sumar", () => {
        it("suma dos positivos", () => {
            expect(sumar(2, 3)).toBe(5);
        });

        it("suma negativos", () => {
            expect(sumar(-1, -1)).toBe(-2);
        });

        it("tipos correctos", () => {
            expectTypeOf(sumar(1, 2)).toBeNumber();
        });
    });

    describe("dividir", () => {
        it("divide correctamente", () => {
            expect(dividir(10, 2)).toBe(5);
        });

        it("lanza error en divisor cero", () => {
            expect(() => dividir(1, 0)).toThrow("Divisor cero");
        });
    });
});
```

### Component Tests (React/Vue/Svelte)

```tsx
// components/Boton.tsx
interface BotonProps {
    onClick: () => void;
    children: React.ReactNode;
    deshabilitado?: boolean;
}

export function Boton({ onClick, children, deshabilitado = false }: BotonProps) {
    return (
        <button onClick={onClick} disabled={deshabilitado} data-testid="boton">
            {children}
        </button>
    );
}

// components/Boton.test.tsx
import { render, screen, fireEvent } from "@testing-library/react";
import { Boton } from "./Boton";
import { describe, it, expect, vi } from "vitest";

describe("Boton", () => {
    it("renderiza texto", () => {
        render(<Boton onClick={() => {}}>Click me</Boton>);
        expect(screen.getByRole("button")).toHaveTextContent("Click me");
    });

    it("llama onClick al clickear", () => {
        const handleClick = vi.fn();
        render(<Boton onClick={handleClick}>Click</Boton>);
        fireEvent.click(screen.getByRole("button"));
        expect(handleClick).toHaveBeenCalledTimes(1);
    });

    it("deshabilitado previene click", () => {
        const handleClick = vi.fn();
        render(<Boton onClick={handleClick} deshabilitado>Click</Boton>);
        fireEvent.click(screen.getByRole("button"));
        expect(handleClick).not.toHaveBeenCalled();
    });
});
```

### Integración: API + DB

```typescript
// test/integration/api.test.ts
import { describe, it, expect, beforeAll, afterAll, beforeEach } from "vitest";
import { app } from "../../src/app";
import { prisma } from "../../src/db";
import request from "supertest";

describe("API /tareas", () => {
    beforeAll(async () => {
        await prisma.$connect();
    });

    afterAll(async () => {
        await prisma.$disconnect();
    });

    beforeEach(async () => {
        await prisma.tarea.deleteMany();
    });

    it("POST /tareas crea tarea", async () => {
        const res = await request(app)
            .post("/tareas")
            .send({ titulo: "Test", descripcion: "Desc" })
            .expect(201);

        expect(res.body.titulo).toBe("Test");
        expect(res.body.id).toBeDefined();
    });

    it("GET /tareas lista tareas", async () => {
        await request(app).post("/tareas").send({ titulo: "T1" });
        await request(app).post("/tareas").send({ titulo: "T2" });

        const res = await request(app).get("/tareas").expect(200);
        expect(res.body).toHaveLength(2);
    });
});
```

---

## Testing en Python (pytest)

```bash
pip install pytest pytest-cov pytest-asyncio httpx
```

```python
# tests/test_math.py
import pytest
from src.math import sumar, dividir

def test_sumar():
    assert sumar(2, 3) == 5
    assert sumar(-1, -1) == -2

def test_dividir():
    assert dividir(10, 2) == 5

def test_dividir_por_cero():
    with pytest.raises(ValueError, match="Divisor cero"):
        dividir(1, 0)

# Parametrizado
@pytest.mark.parametrize("a,b,esperado", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
    (100, 200, 300),
])
def test_sumar_parametrizado(a, b, esperado):
    assert sumar(a, b) == expected
```

### Fixtures (Setup/Teardown)

```python
# tests/conftest.py
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from src.models import Base
from src.database import get_db
from src.main import app
from fastapi.testclient import TestClient

@pytest.fixture(scope="session")
def engine():
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    yield engine
    Base.metadata.drop_all(engine)

@pytest.fixture
def db_session(engine):
    Session = sessionmaker(bind=engine)
    session = Session()
    yield session
    session.rollback()
    session.close()

@pytest.fixture
def client(db_session):
    def override_get_db():
        yield db_session
    app.dependency_overrides[get_db] = override_get_db
    yield TestClient(app)
    app.dependency_overrides.clear()

# tests/test_api.py
def test_crear_tarea(client):
    response = client.post("/tareas", json={"titulo": "Test"})
    assert response.status_code == 201
    assert response.json()["titulo"] == "Test"
```

### Mocking

```python
# tests/test_servicio.py
from unittest.mock import Mock, patch
from src.servicios import ServicioExterno

@patch("src.servicios.requests.get")
def test_obtener_usuario(mock_get):
    mock_get.return_value.json.return_value = {"id": 1, "nombre": "Lucas"}
    mock_get.return_value.status_code = 200

    servicio = ServicioExterno()
    usuario = servicio.obtener_usuario(1)

    assert usuario["nombre"] == "Lucas"
    mock_get.assert_called_once_with("https://api.externa.com/usuarios/1")
```

---

## E2E Testing (Playwright)

```bash
npm install -D @playwright/test
npx playwright install
```

```typescript
// e2e/login.spec.ts
import { test, expect } from "@playwright/test";

test.describe("Login", () => {
    test.beforeEach(async ({ page }) => {
        await page.goto("/login");
    });

    test("login exitoso redirige a dashboard", async ({ page }) => {
        await page.fill('[name="email"]', "user@test.com");
        await page.fill('[name="password"]', "password123");
        await page.click('button[type="submit"]');
        await expect(page).toHaveURL("/dashboard");
    });

    test("credenciales inválidas muestra error", async ({ page }) => {
        await page.fill('[name="email"]', "wrong@test.com");
        await page.fill('[name="password"]', "wrong");
        await page.click('button[type="submit"]');
        await expect(page.locator(".error")).toContainText("Inválido");
    });
});
```

---

## Cobertura y CI

```bash
# Ver cobertura
npm run test:coverage
# o
pytest --cov=src --cov-report=html
```

```yaml
# .github/workflows/test.yml
name: Test
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: npm ci
      - run: npm run test:run
      - run: npm run test:coverage
      - uses: codecov/codecov-action@v3
```

---

## Buenas Prácticas

| Práctica | Por Qué |
|----------|---------|
| **AAA**: Arrange, Act, Assert | Estructura clara |
| **Un assert por test** (ideal) | Falla = causa obvia |
| **Nombres descriptivos** | `it("debería rechazar email inválido")` |
| **Testea comportamiento, no implementación** | Refactor sin romper tests |
| **Datos de prueba realistas** | Fakers, factories |
| **Tests determinísticos** | Sin fechas aleatorias, IDs fijos |
| **Rápidos y paralelos** | Unit tests < 10ms cada uno |

---

## Qué NO Testear

- Código de terceros (librerías)
- Getters/setters triviales
- CSS/estilos visuales (salvo visual regression)
- Implementación privada (cámbiala sin romper tests)

---

## Cheatsheet Testing

```
┌────────────────────────────────────────────────────────────────┐
│ UNITARIOS              INTEGRACIÓN              E2E            │
│ Vitest/Jest            Supertest + DB           Playwright     │
│ Funciones puras        API endpoints            Flujo usuario  │
│ Componentes UI         Servicios externos       Navegador real │
│ Mocks todo             Mocks selectivos         Sin mocks      │
│ < 10ms/test            < 500ms/test             < 30s/test     │
├────────────────────────────────────────────────────────────────┤
│ ASSERTIONS             MOCKING                  FIXTURES       │
│ expect(fn).toBe()      vi.fn() / Mock           beforeEach     │
│ expect().toEqual()     vi.spyOn()               @pytest.fixture│
│ expect().toThrow()     msw (API mock)           conftest.py    │
│ expect().toHaveBeenCalled()                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Ejercicios

1. **Unit**: Testea `calculadora.ts` con 100% cobertura
2. **Component**: Testea formulario de login (valida, envía, error)
3. **Integration**: CRUD completo de tareas con DB real (testcontainers)
4. **E2E**: Registro → Login → Crear post → Logout
5. **CI**: Configura GitHub Actions que corra tests en PR

---

## Recursos

- [Vitest Docs](https://vitest.dev/) - Rápido, moderno
- [Testing Library](https://testing-library.com/) - Queries accesibles
- [Playwright](https://playwright.dev/) - E2E confiable
- [Pytest Docs](https://docs.pytest.org/) - Python testing
- [TestContainers](https://testcontainers.com/) - DBs reales en tests
- [Kent C. Dodds](https://kentcdodds.com/blog/) - Filosofía testing
- [Testing Trophy](https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications) - Pirámide modernizada

---

## Siguiente Paso

→ [Clean Code](clean-code.md) - Código mantenible
→ [Debugging](debugging.md) - Encontrar y arreglar bugs
→ [CI/CD](cicd.md) - Automatiza deploy