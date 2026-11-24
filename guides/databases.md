# Bases de Datos: SQL, NoSQL y ORMs

## 🎯 Qué Aprenderás
- SQL básico (el 80% que usarás diario)
- Cuándo SQL vs NoSQL
- ORMs modernos (Prisma, SQLAlchemy, Drizzle)
- Diseño de esquemas y migraciones

---

## SQL: El Estándar Universal

### Conceptos Clave
| Concepto | Descripción |
|----------|-------------|
| **Tabla** | Filas (registros) + Columnas (campos) |
| **PK** | Primary Key: identificador único (`id`) |
| **FK** | Foreign Key: referencia a otra tabla |
| **Índice** | Acelera búsquedas (como índice de libro) |
| **Transacción** | Todo o nada (ACID) |

### Comandos Esenciales (DDL + DML)

```sql
-- CREAR TABLA
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    creado_en TIMESTAMP DEFAULT NOW(),
    actualizado_en TIMESTAMP DEFAULT NOW()
);

-- ÍNDICES (para búsquedas frecuentes)
CREATE INDEX idx_usuarios_email ON usuarios(email);

-- INSERTAR
INSERT INTO usuarios (email, nombre, password_hash)
VALUES ('lucas@mail.com', 'Lucas', 'hash123')
RETURNING id;

-- SELECCIONAR
SELECT id, nombre, email FROM usuarios WHERE activo = true;
SELECT * FROM usuarios ORDER BY creado_en DESC LIMIT 10 OFFSET 0;
SELECT COUNT(*) FROM usuarios WHERE creado_en > '2024-01-01';

-- ACTUALIZAR
UPDATE usuarios SET nombre = 'Lucas R.', actualizado_en = NOW()
WHERE id = 1;

-- BORRAR (soft delete preferido)
UPDATE usuarios SET activo = false WHERE id = 1;
-- DELETE FROM usuarios WHERE id = 1;  -- Hard delete

-- JOINS
SELECT u.nombre, p.titulo
FROM usuarios u
LEFT JOIN posts p ON p.usuario_id = u.id
WHERE u.activo = true;

-- AGREGACIONES
SELECT u.nombre, COUNT(p.id) as total_posts
FROM usuarios u
LEFT JOIN posts p ON p.usuario_id = u.id
GROUP BY u.id, u.nombre
HAVING COUNT(p.id) > 5
ORDER BY total_posts DESC;
```

### Tipos de Datos Comunes (PostgreSQL)
```sql
-- Numéricos
SMALLINT, INTEGER, BIGINT          -- Enteros
DECIMAL(10,2), NUMERIC(10,2)       -- Precisión exacta (dinero)
REAL, DOUBLE PRECISION             -- Punto flotante

-- Texto
VARCHAR(n), CHAR(n)                -- Longitud variable/fija
TEXT                               -- Sin límite (preferido en PG)

-- Fecha/Hora
TIMESTAMP, TIMESTAMPTZ             -- Con/sin zona horaria
DATE, TIME

-- Otros
BOOLEAN
UUID                               -- ID único universal
JSONB                              -- JSON binario (indexable)
BYTEA                              -- Binario
```

---

## Relaciones y Diseño

### One-to-One
```sql
-- Perfil extendido (opcional)
CREATE TABLE perfiles (
    usuario_id INTEGER PRIMARY KEY REFERENCES usuarios(id) ON DELETE CASCADE,
    bio TEXT,
    avatar_url VARCHAR(500),
    fecha_nacimiento DATE
);
```

### One-to-Many
```sql
-- Posts de un usuario
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    usuario_id INTEGER NOT NULL REFERENCES usuarios(id) ON DELETE CASCADE,
    titulo VARCHAR(200) NOT NULL,
    contenido TEXT,
    publicado BOOLEAN DEFAULT false,
    creado_en TIMESTAMP DEFAULT NOW()
);
CREATE INDEX idx_posts_usuario ON posts(usuario_id);
```

### Many-to-Many (Tabla Pivote)
```sql
-- Tags en posts
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(50) UNIQUE NOT NULL
);

CREATE TABLE post_tags (
    post_id INTEGER REFERENCES posts(id) ON DELETE CASCADE,
    tag_id INTEGER REFERENCES tags(id) ON DELETE CASCADE,
    PRIMARY KEY (post_id, tag_id)
);
```

---

## NoSQL: Cuándo Usar

| Tipo | Ejemplo | Caso de Uso |
|------|---------|-------------|
| **Documento** | MongoDB | Datos semi-estructurados, jerárquicos, cambio frecuente |
| **Clave-Valor** | Redis | Cache, sesiones, colas, rate limiting |
| **Grafo** | Neo4j | Relaciones complejas (redes sociales, recomendaciones) |
| **Columnar** | Cassandra | Series temporales, logs, analytics masivo |
| **Búsqueda** | Elasticsearch | Full-text search, logs, analytics |

### MongoDB (Ejemplo)
```javascript
// Colección usuarios (documentos)
{
    _id: ObjectId("..."),
    email: "lucas@mail.com",
    nombre: "Lucas",
    roles: ["user", "admin"],
    direcciones: [
        { tipo: "casa", calle: "Av. Siempre Viva 123", ciudad: "CABA" }
    ],
    metadata: { fuente: "google", ult_login: ISODate("2024-01-15") }
}

// Índices
db.usuarios.createIndex({ email: 1 }, { unique: true });
db.usuarios.createIndex({ "direcciones.ciudad": 1 });

// Query
db.usuarios.find({ "roles": "admin", activo: true });
db.usuarios.aggregate([
    { $match: { activo: true } },
    { $group: { _id: "$ciudad", total: { $sum: 1 } } }
]);
```

---

## ORMs: Abstracción Segura

### Por Qué ORM
- **Type safety**: TypeScript/Python types ↔ DB schema
- **Migraciones**: Versionado de esquema
- **Prevención SQL Injection**: Query builders parametrizados
- **Productividad**: Menos SQL manual

---

### Prisma (TypeScript/Node.js) - RECOMENDADO

```prisma
// schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Usuario {
  id            Int       @id @default(autoincrement())
  email         String    @unique
  nombre        String
  passwordHash  String    @map("password_hash")
  activo        Boolean   @default(true)
  creadoEn      DateTime  @default(now()) @map("creado_en")
  actualizadoEn DateTime  @updatedAt @map("actualizado_en")

  posts         Post[]
  perfil        Perfil?

  @@map("usuarios")
}

model Perfil {
  id            Int     @id @default(autoincrement())
  usuarioId     Int     @unique @map("usuario_id")
  bio           String?
  avatarUrl     String? @map("avatar_url")
  fechaNac      DateTime? @map("fecha_nacimiento")
  usuario       Usuario @relation(fields: [usuarioId], references: [id], onDelete: Cascade)

  @@map("perfiles")
}

model Post {
  id          Int      @id @default(autoincrement())
  usuarioId   Int      @map("usuario_id")
  titulo      String
  contenido   String?
  publicado   Boolean  @default(false)
  creadoEn    DateTime @default(now()) @map("creado_en")
  usuario     Usuario  @relation(fields: [usuarioId], references: [id], onDelete: Cascade)
  tags        PostTag[]

  @@index([usuarioId])
  @@map("posts")
}

model Tag {
  id     Int       @id @default(autoincrement())
  nombre String    @unique
  posts  PostTag[]

  @@map("tags")
}

model PostTag {
  postId Int  @map("post_id")
  tagId  Int  @map("tag_id")
  post   Post @relation(fields: [postId], references: [id], onDelete: Cascade)
  tag    Tag  @relation(fields: [tagId], references: [id], onDelete: Cascade)

  @@id([postId, tagId])
  @@map("post_tags")
}
```

```bash
# Comandos Prisma
npx prisma init                    # Inicializar
npx prisma migrate dev --name init # Crear migración + aplicar
npx prisma generate                # Generar cliente
npx prisma studio                  # GUI visual
npx prisma db push                 # Push schema sin migración (dev)
npx prisma migrate reset           # Reset DB + migraciones
```

```typescript
// Uso en código
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

// Crear
const user = await prisma.usuario.create({
    data: { email: "lucas@mail.com", nombre: "Lucas", passwordHash: "hash" }
});

// Leer con relaciones
const users = await prisma.usuario.findMany({
    where: { activo: true },
    include: { posts: { where: { publicado: true } }, perfil: true },
    orderBy: { creadoEn: "desc" },
    take: 10,
    skip: 0
});

// Actualizar
await prisma.usuario.update({
    where: { id: 1 },
    data: { nombre: "Lucas R." }
});

// Transacción
await prisma.$transaction(async (tx) => {
    const user = await tx.usuario.create({ data: {...} });
    await tx.perfil.create({ data: { usuarioId: user.id, bio: "Hola" } });
});

// Raw SQL si necesitas
const result = await prisma.$queryRaw`SELECT * FROM usuarios WHERE id = 1`;
```

---

### SQLAlchemy (Python) - Estándar Python

```python
# models.py
from sqlalchemy import Column, Integer, String, Boolean, DateTime, ForeignKey, Text, func
from sqlalchemy.orm import relationship, declarative_base
from datetime import datetime

Base = declarative_base()

class Usuario(Base):
    __tablename__ = "usuarios"
    id = Column(Integer, primary_key=True, autoincrement=True)
    email = Column(String(255), unique=True, nullable=False)
    nombre = Column(String(100), nullable=False)
    password_hash = Column(String(255), nullable=False)
    activo = Column(Boolean, default=True)
    creado_en = Column(DateTime, default=func.now())
    actualizado_en = Column(DateTime, default=func.now(), onupdate=func.now())

    posts = relationship("Post", back_populates="usuario", cascade="all, delete-orphan")
    perfil = relationship("Perfil", back_populates="usuario", uselist=False, cascade="all, delete-orphan")

class Post(Base):
    __tablename__ = "posts"
    id = Column(Integer, primary_key=True)
    usuario_id = Column(Integer, ForeignKey("usuarios.id", ondelete="CASCADE"), nullable=False)
    titulo = Column(String(200), nullable=False)
    contenido = Column(Text)
    publicado = Column(Boolean, default=False)
    creado_en = Column(DateTime, default=func.now())

    usuario = relationship("Usuario", back_populates="posts")
    tags = relationship("Tag", secondary="post_tags", back_populates="posts")
```

```python
# database.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session
from contextlib import contextmanager

engine = create_engine("postgresql://user:pass@localhost/db", echo=True)
SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)

@contextmanager
def get_db() -> Session:
    db = SessionLocal()
    try:
        yield db
        db.commit()
    except Exception:
        db.rollback()
        raise
    finally:
        db.close()

# Uso
with get_db() as db:
    user = db.query(Usuario).filter(Usuario.email == "lucas@mail.com").first()
    new_user = Usuario(email="nuevo@mail.com", nombre="Nuevo", password_hash="hash")
    db.add(new_user)
```

---

### Drizzle ORM (TypeScript) - Ligero, Rápido

```typescript
// schema.ts
import { pgTable, serial, varchar, boolean, timestamp, text, integer, uniqueIndex } from "drizzle-orm/pg-core";

export const usuarios = pgTable("usuarios", {
    id: serial("id").primaryKey(),
    email: varchar("email", { length: 255 }).notNull().unique(),
    nombre: varchar("nombre", { length: 100 }).notNull(),
    passwordHash: varchar("password_hash", { length: 255 }).notNull(),
    activo: boolean("activo").default(true),
    creadoEn: timestamp("creado_en").defaultNow(),
    actualizadoEn: timestamp("actualizado_en").defaultNow().$onUpdate(() => new Date()),
}, (table) => ({
    emailIdx: uniqueIndex("idx_usuarios_email").on(table.email),
}));

export const posts = pgTable("posts", {
    id: serial("id").primaryKey(),
    usuarioId: integer("usuario_id").notNull().references(() => usuarios.id, { onDelete: "cascade" }),
    titulo: varchar("titulo", { length: 200 }).notNull(),
    contenido: text("contenido"),
    publicado: boolean("publicado").default(false),
    creadoEn: timestamp("creado_en").defaultNow(),
}, (table) => ({
    usuarioIdx: index("idx_posts_usuario").on(table.usuarioId),
}));
```

```typescript
// Uso
import { db } from "./db";
import { usuarios, posts } from "./schema";
import { eq, desc } from "drizzle-orm";

const user = await db.insert(usuarios).values({ email: "lucas@mail.com", nombre: "Lucas", passwordHash: "hash" }).returning();
const allUsers = await db.select().from(usuarios).where(eq(usuarios.activo, true)).orderBy(desc(usuarios.creadoEn)).limit(10);
```

---

## Migraciones: Versiona Tu Esquema

```bash
# Prisma
npx prisma migrate dev --name "add_avatar_to_perfil"

# SQLAlchemy (Alembic)
alembic revision --autogenerate -m "add avatar"
alembic upgrade head

# Drizzle
npx drizzle-kit generate
npx drizzle-kit migrate
```

### Buenas Prácticas Migraciones
1. **Una migración = un cambio lógico**
2. **Nunca edites migraciones aplicadas** (crea nueva)
3. **Testea en staging antes de prod**
4. **Backup antes de migrar en prod**
5. **Migraciones reversibles** (up/down)

---

## Conexión y Pooling

```typescript
// Prisma: pool automático, configurable
const prisma = new PrismaClient({
    datasources: { db: { url: process.env.DATABASE_URL } },
    log: process.env.NODE_ENV === "development" ? ["query", "error"] : ["error"],
});

// Serverless (Vercel, AWS Lambda): connection pooling externo
// Usa PgBouncer, Prisma Data Proxy, o Supabase pooler
```

---

## Cheatsheet SQL

```
┌────────────────────────────────────────────────────────────────┐
│ DDL (Estructura)       DML (Datos)              QUERIES        │
│ CREATE TABLE           INSERT                    SELECT        │
│ ALTER TABLE            UPDATE                    WHERE          │
│ DROP TABLE             DELETE                    JOIN          │
│ CREATE INDEX           TRUNCATE                  GROUP BY      │
│                        MERGE/UPSERT              HAVING        │
├────────────────────────────────────────────────────────────────┤
│ JOINS                  AGREGACIONES             SUBCONSULTAS   │
│ INNER JOIN             COUNT/SUM/AVG             IN / EXISTS   │
│ LEFT JOIN              MIN/MAX                   Correlacionadas│
│ RIGHT JOIN             GROUP BY                  CTE (WITH)    │
│ FULL OUTER             HAVING                    Window Funcs  │
├────────────────────────────────────────────────────────────────┤
│ ORM RECOMENDADOS       NO SQL                   HERRAMIENTAS   │
│ Prisma (TS)            MongoDB (doc)            pgAdmin        │
│ SQLAlchemy (Py)        Redis (kv)               DBeaver        │
│ Drizzle (TS)           Elasticsearch            TablePlus      │
│ TypeORM (TS)           Neo4j (graph)            Prisma Studio  │
└────────────────────────────────────────────────────────────────┘
```

---

## Ejercicios

1. **Diseña**: Esquema para blog (usuarios, posts, comentarios, tags, likes)
2. **SQL**: Escribe queries: posts con autor, top 5 autores, tags más usados
3. **Prisma**: Inicializa proyecto, define schema, corre migración, seed data
4. **Relaciones**: Implementa many-to-many posts↔tags con Prisma
5. **Transacción**: Crea usuario + perfil + post inicial en una transacción
6. **Índices**: Analiza query plan (`EXPLAIN ANALYZE`) y agrega índices

---

## Recursos

- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) - Excelente
- [Use The Index, Luke](https://use-the-index-luke.com/) - Índices explicados
- [Prisma Docs](https://www.prisma.io/docs/) - Mejor ORM TypeScript
- [SQLAlchemy Tutorial](https://docs.sqlalchemy.org/en/20/tutorial/) - Python
- [Drizzle ORM](https://orm.drizzle.team/) - Ligero y rápido
- [DBDiagram.io](https://dbdiagram.io/) - Diseña esquemas visual
- [SQL Zoo](https://sqlzoo.net/) - Práctica interactiva

---

## Siguiente Paso

→ [Testing](testing.md) - Unit, Integration, E2E
→ [Clean Code](clean-code.md) - Código mantenible
→ [Docker](docker.md) - Contenedores para DBs y apps