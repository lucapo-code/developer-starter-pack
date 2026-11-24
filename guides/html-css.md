# HTML & CSS: Bases del Desarrollo Web

## 🎯 Objetivo
Entender cómo funcionan las páginas web y crear tus primeras páginas estáticas.

---

## HTML: Estructura

### Esqueleto Básico
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Primera Página</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <header>
        <h1>Mi Sitio Web</h1>
        <nav>
            <ul>
                <li><a href="#inicio">Inicio</a></li>
                <li><a href="#acerca">Acerca</a></li>
                <li><a href="#contacto">Contacto</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <section id="inicio">
            <h2>Bienvenidos</h2>
            <p>Esta es mi primera página web.</p>
            <img src="foto.jpg" alt="Mi foto" width="300">
        </section>

        <section id="acerca">
            <h2>Acerca de Mí</h2>
            <article>
                <h3>Desarrollador Junior</h3>
                <p>Aprendo HTML, CSS y JavaScript.</p>
            </article>
        </section>
    </main>

    <footer>
        <p>&copy; 2024 Mi Nombre. Hecho con ❤️</p>
    </footer>

    <script src="script.js"></script>
</body>
</html>
```

### Etiquetas Semánticas (HTML5)
| Etiqueta | Uso |
|----------|-----|
| `<header>` | Cabecera de página/sección |
| `<nav>` | Navegación principal |
| `<main>` | Contenido principal (una por página) |
| `<section>` | Sección temática |
| `<article>` | Contenido independiente (post, noticia) |
| `<aside>` | Contenido lateral/relacionado |
| `<footer>` | Pie de página/sección |
| `<figure>` + `<figcaption>` | Imagen con leyenda |

### Etiquetas Comunes
```html
<!-- Texto -->
<h1> - <h6>          <!-- Títulos (h1 = principal, solo uno) -->
<p>                   <!-- Párrafo -->
<strong> / <b>       <!-- Negrita (semántica / visual) -->
<em> / <i>           <!-- Cursiva (semántica / visual) -->
<a href="url">        <!-- Enlace -->
<br>                  <!-- Salto de línea -->
<hr>                  <!-- Línea horizontal -->

<!-- Listas -->
<ul>                  <!-- No ordenada -->
<ol>                  <!-- Ordenada -->
<li>                  <!-- Item -->

<!-- Multimedia -->
<img src="img.jpg" alt="descripción" width="300" height="200">
<video src="video.mp4" controls></video>
<audio src="audio.mp3" controls></audio>

<!-- Formularios -->
<form action="/enviar" method="POST">
    <label for="nombre">Nombre:</label>
    <input type="text" id="nombre" name="nombre" required>
    
    <label for="email">Email:</label>
    <input type="email" id="email" name="email">
    
    <textarea name="mensaje" rows="4"></textarea>
    
    <button type="submit">Enviar</button>
</form>

<!-- Tablas (solo para datos tabulares) -->
<table>
    <thead>
        <tr><th>Producto</th><th>Precio</th></tr>
    </thead>
    <tbody>
        <tr><td>Laptop</td><td>$999</td></tr>
    </tbody>
</table>
```

---

## CSS: Estilos

### Formas de Aplicar CSS
```html
<!-- 1. Inline (evitar) -->
<h1 style="color: red;">Título</h1>

<!-- 2. Interno (en <head>) -->
<style>
    h1 { color: red; }
</style>

<!-- 3. Externo (RECOMENDADO) -->
<link rel="stylesheet" href="styles.css">
```

### Selectores Básicos
```css
/* Elemento */
h1 { color: #333; }

/* Clase (reutilizable) */
.titulo-principal { font-size: 2rem; }
.boton { background: blue; color: white; }

/* ID (único por página) */
#header { position: fixed; }

/* Descendiente */
nav ul { list-style: none; }

/* Hijo directo */
main > section { margin: 1rem 0; }

/* Pseudo-clases */
a:hover { color: orange; }
input:focus { outline: 2px solid blue; }
li:nth-child(odd) { background: #f5f5f5; }

/* Atributo */
input[type="email"] { border-color: green; }
```

### Modelo de Caja (Box Model)
```
┌─────────────────────────────────────┐
│           MARGIN (externo)          │
│  ┌───────────────────────────────┐  │
│  │      BORDER (borde)           │  │
│  │  ┌─────────────────────────┐  │  │
│  │  │   PADDING (interno)     │  │  │
│  │  │  ┌───────────────────┐  │  │  │
│  │  │  │  CONTENT (contenido) │  │  │
│  │  │  └───────────────────┘  │  │  │
│  │  └─────────────────────────┘  │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

```css
.caja {
    width: 300px;
    height: 200px;
    padding: 20px;        /* Espacio interno */
    border: 2px solid #333; /* Borde */
    margin: 10px;         /* Espacio externo */
    box-sizing: border-box; /* Incluye padding+border en width */
}
```

---

## Flexbox: Layout Moderno (1 Dimensión)

```css
.contenedor {
    display: flex;
    flex-direction: row;      /* row | column | row-reverse | column-reverse */
    justify-content: center;  /* main axis: center | space-between | space-around | flex-start | flex-end */
    align-items: center;      /* cross axis: center | stretch | flex-start | flex-end */
    gap: 1rem;                /* Espacio entre items */
    flex-wrap: wrap;          /* Permite salto de línea */
}

/* Items flex */
.item {
    flex: 1;                  /* flex-grow | flex-shrink | flex-basis */
    /* flex: 1 = crece, encoge, base 0 */
    /* flex: 0 0 200px = no crece, no encoge, 200px base */
    align-self: stretch;      /* Override align-items para este item */
}
```

### Casos Comunes Flexbox
```css
/* Centrado perfecto */
.centrado {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
}

/* Navbar: logo izquierda, links derecha */
.navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

/* Card grid responsivo */
.grid {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
}
.card {
    flex: 1 1 300px;  /* Mínimo 300px, crece si hay espacio */
}
```

---

## Grid: Layout 2 Dimensiones

```css
.grid-container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);  /* 3 columnas iguales */
    grid-template-rows: auto 1fr auto;       /* header, main, footer */
    gap: 1rem;
    grid-template-areas:
        "header header header"
        "sidebar main main"
        "footer footer footer";
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main { grid-area: main; }
.footer { grid-area: footer; }

/* Responsivo */
@media (max-width: 768px) {
    .grid-container {
        grid-template-areas:
            "header"
            "main"
            "sidebar"
            "footer";
        grid-template-columns: 1fr;
    }
}
```

---

## Unidades CSS

| Unidad | Tipo | Uso |
|--------|------|-----|
| `px` | Absoluta | Bordes, sombras, iconos |
| `rem` | Relativa (root) | **Tamaños base, spacing** |
| `em` | Relativa (padre) | Tamaños dentro de componentes |
| `%` | Relativa (padre) | Anchos, heights fluidos |
| `vw/vh` | Viewport | Full screen, hero sections |
| `fr` | Grid | Fracción de espacio libre |

> **Regla**: Usa `rem` para casi todo. `1rem = 16px` (por defecto).

---

## Variables CSS (Custom Properties)

```css
:root {
    --color-primary: #2563eb;
    --color-secondary: #1e40af;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 2rem;
    --radius: 8px;
    --shadow: 0 4px 6px rgba(0,0,0,0.1);
    --font-sans: system-ui, sans-serif;
}

.boton {
    background: var(--color-primary);
    padding: var(--spacing-sm) var(--spacing-md);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    font-family: var(--font-sans);
}

/* Override en componente */
.boton--secundario {
    --color-primary: #64748b;
}
```

---

## Media Queries (Responsive)

```css
/* Mobile First: base = móvil, luego tablet, luego desktop */

/* Tablet */
@media (min-width: 768px) {
    .grid { grid-template-columns: repeat(2, 1fr); }
    h1 { font-size: 3rem; }
}

/* Desktop */
@media (min-width: 1024px) {
    .grid { grid-template-columns: repeat(3, 1fr); }
    .container { max-width: 1200px; margin: 0 auto; }
}

/* Pantallas grandes */
@media (min-width: 1440px) {
    .container { max-width: 1400px; }
}
```

### Breakpoints Comunes
```css
/* Tailwind-style */
@media (min-width: 640px)  { /* sm */ }
@media (min-width: 768px)  { /* md */ }
@media (min-width: 1024px) { /* lg */ }
@media (min-width: 1280px) { /* xl */ }
@media (min-width: 1536px) { /* 2xl */ }
```

---

## Animaciones y Transiciones

```css
/* Transición simple */
.boton {
    background: var(--color-primary);
    transition: all 0.2s ease;
}
.boton:hover {
    background: var(--color-secondary);
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
}

/* Keyframes */
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
}

@keyframes spin {
    to { transform: rotate(360deg); }
}

.card {
    animation: fadeIn 0.3s ease-out;
}

.spinner {
    animation: spin 1s linear infinite;
}
```

---

## Buenas Prácticas CSS

```css
/* 1. Reset básico */
*, *::before, *::after {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

html {
    font-size: 16px;        /* Base para rem */
    scroll-behavior: smooth;
}

body {
    font-family: var(--font-sans);
    line-height: 1.6;
    color: var(--color-text);
    background: var(--color-bg);
}

/* 2. Clases utilitarias */
.sr-only {                  /* Solo lectores de pantalla */
    position: absolute;
    width: 1px; height: 1px;
    padding: 0; margin: -1px;
    overflow: hidden; clip: rect(0,0,0,0);
    white-space: nowrap; border: 0;
}

.container {
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 var(--spacing-md);
}

/* 3. Organiza por: */
/* - Reset & base */
/* - Variables */
/* - Layout (grid, flex) */
/* - Componentes (botones, cards, forms) */
/* - Utilidades */
/* - Media queries al final de cada sección */
```

---

## Ejercicio Práctico: Landing Page

Crea `index.html` + `styles.css`:

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Portafolio</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <header class="hero">
        <nav class="nav container">
            <a href="#" class="logo">Dev<span>.</span></a>
            <ul class="nav-links">
                <li><a href="#proyectos">Proyectos</a></li>
                <li><a href="#contacto">Contacto</a></li>
            </ul>
        </nav>
        <div class="hero-content container">
            <h1>Hola, soy <span class="highlight">Desarrollador</span></h1>
            <p>Creo experiencias web modernas y accesibles</p>
            <a href="#proyectos" class="btn btn-primary">Ver mi trabajo</a>
        </div>
    </header>

    <main>
        <section id="proyectos" class="section container">
            <h2>Proyectos Destacados</h2>
            <div class="projects-grid">
                <article class="card">
                    <img src="proj1.jpg" alt="Proyecto 1">
                    <div class="card-body">
                        <h3>E-commerce</h3>
                        <p>Tienda online con carrito y pagos</p>
                        <span class="tag">React</span>
                        <span class="tag">Node</span>
                    </div>
                </article>
                <!-- Repite para más proyectos -->
            </div>
        </section>

        <section id="contacto" class="section container">
            <h2>Contacto</h2>
            <form class="form">
                <div class="form-group">
                    <label for="email">Email</label>
                    <input type="email" id="email" required>
                </div>
                <button type="submit" class="btn btn-primary">Enviar</button>
            </form>
        </section>
    </main>

    <footer class="footer">
        <p>&copy; 2024 Tu Nombre</p>
    </footer>
</body>
</html>
```

```css
/* styles.css */
:root {
    --primary: #2563eb;
    --primary-dark: #1d4ed8;
    --text: #1f2937;
    --text-light: #6b7280;
    --bg: #ffffff;
    --bg-alt: #f9fafb;
    --spacing: 1rem;
    --radius: 8px;
    --shadow: 0 4px 6px -1px rgba(0,0,0,0.1);
    --max-width: 1100px;
}

*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { font-size: 16px; scroll-behavior: smooth; }
body { font-family: system-ui, sans-serif; line-height: 1.6; color: var(--text); background: var(--bg); }

.container { width: 100%; max-width: var(--max-width); margin: 0 auto; padding: 0 var(--spacing); }

/* Nav */
.nav { display: flex; justify-content: space-between; align-items: center; padding: var(--spacing) 0; }
.logo { font-weight: 700; font-size: 1.25rem; color: var(--primary); text-decoration: none; }
.logo span { color: var(--text); }
.nav-links { display: flex; gap: 2rem; list-style: none; }
.nav-links a { text-decoration: none; color: var(--text); font-weight: 500; transition: color 0.2s; }
.nav-links a:hover { color: var(--primary); }

/* Hero */
.hero { min-height: 100vh; display: flex; flex-direction: column; justify-content: center; background: linear-gradient(135deg, var(--bg) 0%, var(--bg-alt) 100%); }
.hero-content { text-align: center; padding: 2rem 0; }
.hero h1 { font-size: clamp(2rem, 5vw, 3.5rem); line-height: 1.2; margin-bottom: 1rem; }
.highlight { color: var(--primary); }
.hero p { font-size: 1.25rem; color: var(--text-light); margin-bottom: 2rem; max-width: 600px; margin-left: auto; margin-right: auto; }

/* Botones */
.btn { display: inline-block; padding: 0.75rem 1.5rem; border-radius: var(--radius); text-decoration: none; font-weight: 600; transition: all 0.2s; border: none; cursor: pointer; }
.btn-primary { background: var(--primary); color: white; }
.btn-primary:hover { background: var(--primary-dark); transform: translateY(-2px); box-shadow: var(--shadow); }

/* Sections */
.section { padding: 5rem 0; }
.section h2 { font-size: 2rem; text-align: center; margin-bottom: 3rem; position: relative; }
.section h2::after { content: ''; display: block; width: 60px; height: 4px; background: var(--primary); margin: 0.5rem auto 0; border-radius: 2px; }

/* Projects Grid */
.projects-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 2rem; }
.card { background: var(--bg); border-radius: var(--radius); box-shadow: var(--shadow); overflow: hidden; transition: transform 0.2s, box-shadow 0.2s; }
.card:hover { transform: translateY(-4px); box-shadow: 0 10px 20px rgba(0,0,0,0.15); }
.card img { width: 100%; height: 200px; object-fit: cover; }
.card-body { padding: 1.5rem; }
.card h3 { margin-bottom: 0.5rem; }
.card p { color: var(--text-light); margin-bottom: 1rem; }
.tag { display: inline-block; background: var(--bg-alt); padding: 0.25rem 0.75rem; border-radius: 999px; font-size: 0.875rem; margin-right: 0.5rem; color: var(--primary); }

/* Form */
.form { max-width: 500px; margin: 0 auto; display: flex; flex-direction: column; gap: 1rem; }
.form-group { display: flex; flex-direction: column; gap: 0.5rem; }
.form-group label { font-weight: 500; }
.form-group input { padding: 0.75rem; border: 1px solid #d1d5db; border-radius: var(--radius); font-size: 1rem; }
.form-group input:focus { outline: none; border-color: var(--primary); box-shadow: 0 0 0 3px rgba(37,99,235,0.2); }

/* Footer */
.footer { background: var(--text); color: white; text-align: center; padding: 2rem 0; margin-top: auto; }

/* Responsive */
@media (max-width: 768px) {
    .nav-links { display: none; } /* En real: hamburger menu */
    .hero { min-height: auto; padding: 4rem 0; }
    .section { padding: 3rem 0; }
}
```

---

## Cheatsheet CSS

```
┌────────────────────────────────────────────────────────────────┐
│ SELECTORES           FLEXBOX              GRID                 │
│ .clase               display: flex        display: grid        │
│ #id                  gap: 1rem            grid-template-cols   │
│ elemento             justify-content      grid-template-areas  │
│ [attr]               align-items          gap                   │
│ :hover               flex: 1              fr unit              │
├────────────────────────────────────────────────────────────────┤
│ BOX MODEL            UNIDADES             RESPONSIVE           │
│ padding: 1rem        rem (root em)        @media (min: 768px)  │
│ margin: auto         em (parent em)       Mobile First         │
│ border: 1px solid    % (parent)           clamp()              │
│ box-sizing: border   vw/vh (viewport)     auto-fit/minmax      │
├────────────────────────────────────────────────────────────────┤
│ VARIABLES            ANIMACIONES          BUENAS PRÁCTICAS     │
│ --color: #fff        transition: .2s      Mobile First         │
│ var(--color)         @keyframes           BEM naming           │
│ :root { }            transform            DRY (no repetir)     │
└────────────────────────────────────────────────────────────────┘
```

---

## Recursos

- [MDN HTML](https://developer.mozilla.org/es/docs/Web/HTML) - Referencia oficial
- [MDN CSS](https://developer.mozilla.org/es/docs/Web/CSS) - Referencia oficial
- [CSS Tricks Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) - Guía visual
- [CSS Tricks Grid](https://css-tricks.com/snippets/css/complete-guide-grid/) - Guía visual
- [Every Layout](https://every-layout.dev/) - Patrones de layout
- [Josh Comeau CSS](https://www.joshwcomeau.com/css/) - Artículos profundos
- [Kevin Powell](https://www.youtube.com/@KevinPowell) - YouTube CSS

---

## Siguiente Paso

→ [JavaScript Moderno](javascript.md) - Interactividad
→ [Terminal](terminal.md) - Si aún no lo dominas