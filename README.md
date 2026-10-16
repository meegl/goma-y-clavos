# Mesa de tangentes

Editor visual para componer formas geométricas con círculos de radio editable, unidos por cápsulas tangentes y empalmes cóncavos. Exporta a **SVG por capas** (para cualquier programa de vectores) o **PNG**.

**Probarlo:** https://meegl.github.io/mesa-de-tangentes/

## Uso

- **Mover**: arrastra círculos; arrastra el asa azul para cambiar el radio. Doble clic en un hueco crea un círculo.
- **Añadir**: un clic crea un círculo.
- **Unir**: pulsa dos círculos para unirlos con una cápsula tangente; la × del centro de la unión la quita.
- **Radio de empalme**: redondea las zonas cóncavas entre círculos.
- **Aleatorio** (`R`), **Vista limpia** (`P`), deshacer/rehacer (`Ctrl+Z`).
- Exporta capas con nombre: `fondo`, `rejilla`, `forma`, `circulos-construccion`, `textos`. Por defecto solo `fondo` y `forma`.

## Cómo está hecho

Un único `index.html`, sin dependencias ni paso de compilación. El contorno se calcula de forma exacta (arcos y rectas): se dilata la unión de círculos y cápsulas, se halla su contorno y se vuelve a erosionar, lo que produce los empalmes cóncavos como arcos de radio constante.

Licencia: MIT.
