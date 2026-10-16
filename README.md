# Goma y clavos

Editor visual para componer formas geométricas con círculos de radio editable, rodeados por un contorno de tangentes, como una goma elástica alrededor de clavos. Exporta a **SVG por capas** (para cualquier programa de vectores) o **PNG**.

Nace de [Mesa de tangentes](https://meegl.github.io/mesa-de-tangentes/): en lugar de unir círculos con cápsulas, una goma elástica los recorre en orden y los círculos negativos la empujan desde fuera.

## Uso

- **Mover** (`V`): arrastra círculos; arrastra el asa azul para cambiar el radio. Doble clic en un hueco crea un círculo.
- **Añadir** (`A`): un clic crea un círculo y lo mete en el contorno, detrás del seleccionado o donde menos lo alarga.
- **Contorno** (`O`): pulsa un círculo del contorno para elegir dónde insertar y luego los de fuera para meterlos en orden. Los círculos que están fuera del contorno se ven discontinuos.
- **Negativo** (`N`): el círculo empuja la goma desde fuera y deja un arco cóncavo. Entre círculos de distinto signo, la recta tangente es cruzada.
- **En el contorno**: en el panel del círculo seleccionado, lo mete o lo saca del contorno sin borrarlo.
- **Aleatorio** (`R`), deshacer/rehacer (`Ctrl+Z`).
- **Capas visibles**: rejilla, círculos de construcción, marcas + / − y guías (`G`). Se exporta lo que ves, salvo las guías, en capas con nombre: `fondo`, `rejilla`, `forma`, `circulos-construccion`, `marcas`.

## Cómo está hecho

Un único `index.html`, sin dependencias ni paso de compilación. El contorno se calcula de forma exacta (arcos y rectas): entre cada par de círculos consecutivos se traza la tangente común (exterior si tienen el mismo signo, cruzada si no) y se unen con arcos de cada círculo, convexos en los positivos y cóncavos en los negativos.

Licencia: MIT.
