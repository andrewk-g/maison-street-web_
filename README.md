 Maison Street · Sitio web de un negocio local

**Nombre:** camilo andres gonzalez 
**Fecha:** 12 de octubre de 2026  
**Sitio publicado:** https://andrewk-g.github.io/maison-street/ 

## 1. El proyecto

Maison Street es una marca de ropa urbana (streetwear) que hoy vende por WhatsApp e Instagram (@maisonstreet_x). El sitio es para sus clientes de todo Colombia: personas jóvenes que quieren ver las prendas, comparar precios y pedir rápido desde el celular.

El cliente necesita un sitio sencillo que se vea bien en 360 px, que muestre productos y precios, transmita confianza con opiniones y permita pedir por formulario o por WhatsApp. Los envíos son solo a nivel nacional.

## 2. Estructura

```
maison-street/
├── index.html
├── pedidos.html
├── README.md
├── css/styles.css
├── images/
└── capturas/
```

- `index.html`: presentación, productos, cómo pedir, opiniones y envíos.
- `pedidos.html`: tabla de precios y formulario de pedido.
- Las dos páginas comparten el mismo `<header>`, `<nav>` y `<footer>`.

Etiquetas semánticas y por qué:

| Etiqueta | Dónde | Por qué |
|---|---|---|
| `header` + `nav` | Parte superior | Contiene el logo y el menú principal, que se repite en las dos páginas. |
| `main` | Contenido | Un solo `main` por página con el contenido propio de cada una. |
| `section` | Productos, Cómo pedir, Opiniones, Envíos | Cada una es un bloque temático con su `h2`. |
| `article` | Tarjetas de producto y reseñas | Cada una tiene sentido completo por sí sola. |
| `aside` | Aviso de envíos nacionales | Es información lateral al flujo de pasos. |
| `blockquote` + `cite` | Opiniones | Son citas de clientes con su autor. |
| `footer` | Parte inferior | Contacto y créditos. |

Hay un solo `h1` por página: "Ropa urbana hecha para la calle" en `index.html` y "Precios y pedidos" en `pedidos.html`.

## 3. Sistema visual

Todo vive en variables dentro de `:root` (`css/styles.css`):

```css
--tinta: #16181d;   --hueso: #f1efe8;   --azul: #2340ff;
--fuente-titulo: "Bebas Neue", Impact, "Arial Narrow", sans-serif;
--espacio-m: 1rem;  --radio: 0.75rem;
```

- **Colores:** tinta casi negra y hueso por ser la base del streetwear (contraste alto y legible). El azul `#2340ff` es el único acento: marca precios, enlaces y foco.
- **Fuentes:** Bebas Neue (condensada y fuerte) para títulos, Inter para texto. Cada una tiene respaldo por si no carga.
- **Espacios y radio:** `--espacio-s/m/l` y `--radio` mantienen todos los márgenes y esquinas coherentes.

## 4. Especificidad

### Conflicto 1: el precio de la tarjeta

Reglas que compiten sobre el mismo `<p class="tarjeta__precio">`:

```
.tarjeta p                  → (0,1,1)  color: var(--tinta-suave)
.tarjeta .tarjeta__precio   → (0,2,0)  color: var(--azul)
```

Gana `.tarjeta .tarjeta__precio` porque tiene 2 clases contra 1 clase y 1 etiqueta. No importa el orden. Al principio escribí solo `.tarjeta__precio` (0,1,0) y perdía contra `.tarjeta p`.

![Conflicto 1](capturas/conflicto-1.png)

### Conflicto 2: el aviso de envíos (empate resuelto por orden)

Reglas que compiten sobre `<aside class="aviso aviso--envio">`:

```
.aviso        → (0,1,0)  background: var(--hueso-oscuro)
.aviso--envio → (0,1,0)  background: var(--azul-alfa)
```

Empatan (1 clase cada una). Gana `.aviso--envio` porque está **después** en el archivo; si la moviera arriba de `.aviso`, perdería.

![Conflicto 2](capturas/conflicto-2.png)

### Conflicto 3: la fila destacada de la tabla

Reglas que compiten sobre la fila "Conjuntos (promo)", que es la fila 4 del `tbody` (par):

```
.tabla tr:nth-child(even)                  → (0,2,1)  background: var(--hueso)
.tabla tbody tr.tabla__fila--destacada     → (0,2,2)  background: var(--azul-alfa)
```

La primera suma 1 clase + 1 pseudoclase + 1 etiqueta; la segunda, 2 clases + 2 etiquetas. Ambas tienen 2 en clases, pero la segunda gana en etiquetas (2 contra 1).

![Conflicto 3](capturas/conflicto-3.png)

## 5. Herencia

- **Se hereda:** `color`. En `.hero` pongo `color: var(--hueso)` y el `h1` y el párrafo de la portada lo reciben sin repetirlo. También se heredan `font-family` y `overflow-wrap`, puestos en `body`.
- **No se hereda:** `border`. El borde de `.tarjeta` no pasa al `h3`, a los `p` ni a la imagen de adentro. Otro caso: `input`, `select` y `button` no heredan la fuente del `body`, por eso uso `font: inherit` en `.formulario__campo` y `.boton`.

## 6. Modelo de cajas

Ancho total de `.tarjeta` en escritorio (`max-width: 20rem`, `padding: 1.25rem`, borde izquierdo y derecho de 2px, `box-sizing` por defecto `content-box`):

```
contenido  = 20rem       = 320 px
padding    = 1.25rem × 2 =  40 px
borde      = 2px × 2     =   4 px
total                    = 364 px
```

A 360 px de pantalla: el espacio disponible es 360 − 32 (padding de `.seccion`) = 328 px. Con el `@media`, la tarjeta usa padding de 1rem: contenido = 328 − 32 − 4 = **292 px**, sin desborde.

Márgenes que se solapan: `.seccion__titulo` tiene `margin-bottom: 1.5rem` (24 px) y el párrafo `.seccion__intro` que le sigue tiene `margin-top: 1rem` (16 px). Entre los dos queda **24 px**, no 40: los márgenes verticales adyacentes se solapan y gana el mayor.

## 7. Selectores

`.tabla tr:nth-child(even)` cuenta las posiciones de cada `<tr>` **entre sus hermanos dentro de su mismo contenedor**. El `thead` tiene 1 fila y el `tfoot` 1 fila (posición 1, no son pares). El `tbody` tiene 7 filas y se aplica en la 2, la 4 y la 6: Camisetas, Conjuntos y Pantalonetas. La fila 4 (Conjuntos) además recibe `.tabla__fila--destacada`, que le gana (conflicto 3).

Otros selectores: `a[target="_blank"]` (atributo), `.tarjeta + .tarjeta` (hermano adyacente), `.menu__lista > li` (hijo directo), `::before` en las reseñas y `::after` en los títulos.

## 8. Responsive

El `@media (max-width: 40rem)` está al final de la hoja para ganar por orden. Cambia:

- `.hero__titulo` baja de 4rem a 2.75rem para que no desborde.
- El menú pasa de enlaces en línea a una lista vertical, más fácil de tocar.
- `.tarjeta` y las celdas de la tabla reducen su padding para aprovechar los 360 px.

Además, la tabla va dentro de `.tabla-contenedor` con `overflow-x: auto` por si una fila no cabe.

## 9. Problemas y soluciones

1. **Los campos del formulario se salían de la pantalla a 360 px.** Tenían `width: 100%` más padding y borde, y con `content-box` el total pasaba del 100%. Lo arreglé con `box-sizing: border-box` en `.formulario__campo`.
2. **El botón y los campos tenían otra letra que el resto del sitio.** Los controles de formulario no heredan la fuente. Lo arreglé con `font: inherit`.
3. **El precio de las tarjetas salía gris, no azul.** Mi regla `.tarjeta__precio` (0,1,0) perdía contra `.tarjeta p` (0,1,1). Subí la especificidad a `.tarjeta .tarjeta__precio` (0,2,0).

## 10. Capturas

| | Escritorio | 360 px |
|---|---|---|
| `index.html` | ![](capturas/index-escritorio.png) | ![](capturas/index-360.png) |
| `pedidos.html` | ![](capturas/pedidos-escritorio.png) | ![](capturas/pedidos-360.png) |
