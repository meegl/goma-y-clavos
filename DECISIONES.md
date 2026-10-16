# Decisiones de Goma y clavos

Registro de por qué el programa es como es. Si cambias algo de lo que aquí se describe, actualiza este archivo.

- **Web:** https://meegl.github.io/goma-y-clavos/
- **Repositorio:** https://github.com/meegl/goma-y-clavos
- **Origen:** nace de [Mesa de tangentes](https://github.com/meegl/mesa-de-tangentes) (octubre de 2026). Ese programa sigue existiendo aparte y no se toca desde aquí.

## 1. Por qué existe

Mesa de tangentes une círculos con **cápsulas** (tangentes exteriores) y redondea las zonas cóncavas con **un único radio de empalme automático**. La pregunta fue si con eso se podía reproducir un póster de referencia (TANGENT series.012, de Polyworks Studio): 9 círculos «+» y 7 círculos «−» sobre una rejilla.

**Conclusión: no se puede.** Al superponer la rejilla sobre el póster se vio que:

- Los «−» no son empalmes que salen solos: están **colocados a mano** en la rejilla, igual que los «+».
- Hay **tangentes cruzadas**, por ejemplo una recta que va de lo alto de un «+» a lo bajo de otro y cambia de lado el negro. Las cápsulas solo dan tangentes exteriores.
- Hay cuellos más finos que cualquier círculo y brazos que pasan entre dos «−».

Un intento con el motor antiguo (círculos ocultos y empalme igual al radio) cubría los «−» de negro y ningún empalme caía en su sitio.

## 2. El modelo: una goma elástica alrededor de clavos

- La forma es **un único contorno cerrado** que recorre círculos **en orden**.
- Cada círculo tiene signo:
  - **Positivo:** queda dentro de la goma y da un arco convexo.
  - **Negativo:** empuja la goma desde fuera y da un arco cóncavo.
- Entre dos círculos consecutivos va la tangente común:
  - **exterior** si tienen el mismo signo;
  - **cruzada** si tienen signo distinto.
- No hay esquinas, así que **no hay radio de empalme**: dejó de tener sentido y se quitó.

**Validación:** el póster se reproduce exactamente con 16 círculos (ver §8). Coinciden el cuello fino y las tangentes cruzadas.

### Geometría (módulo `TB` en `index.html`)

- **Radio con signo:** ρ = +r para un positivo, −r para un negativo.
- **Sentido del recorrido:** se normaliza a **horario en pantalla** (y hacia abajo). Se usa el área del polígono de centros y, si sale negativa, se invierte el orden.
- **Tangente de A a B:** con u = (B−A)/L, v = (u.y, −u.x) y a = −(ρB−ρA)/L, la normal exterior es n = a·u + √(1−a²)·v. Los puntos de tangencia son A + ρA·n y B + ρB·n.
- **Tangente imposible:** si |a| ≥ 1, los dos círculos se tocan o se solapan. En ese caso se devuelve `bad` con los dos índices y la interfaz los marca en rojo.
- **Arcos:** en cada círculo, el arco va del punto de llegada al de salida. En un positivo, en el sentido del recorrido (sweep 1); en un negativo, al revés (sweep 0).
- **Relleno `nonzero`** en el lienzo y en el SVG, para que una goma que se cruce no abra agujeros raros.
- `solve` devuelve también `segs`, las tangentes, que Aleatorio usa para descartar contornos que se cruzan.

## 3. Lo que se quitó del programa original

- La herramienta **Unir**, las aristas (cápsulas) y el **radio de empalme**.
- El **motor geométrico antiguo** (dilatar, contornear y erosionar), unas 500 líneas.
- **Vista limpia** y el botón **Guías**, sustituidos por «Capas visibles» (ver §5).
- Los **textos de las esquinas** (TANGENT series… / no.…) y sus campos. El usuario no les veía sentido.

## 4. Interacción

- **Mover (`V`):** arrastrar círculos; el asa azul cambia el radio. Doble clic en un hueco crea un círculo, igual que Añadir.
- **Añadir (`A`):**
  - Clic en un hueco: crea un positivo y **lo mete en el contorno**.
  - Clic en un círculo: lo selecciona como punto de inserción.
  - *Antes, el círculo nuevo quedaba fuera del contorno y era invisible; ese fue el fallo de «Añadir no funciona».*
- **Contorno (`O`):**
  - Clic en un círculo **de fuera:** lo inserta.
  - Clic en uno **del contorno:** solo lo selecciona, para elegir el punto de inserción. *Antes lo duplicaba al final y deformaba la figura.*
- **Regla de inserción** (`insertInLoop`): detrás del círculo seleccionado si está en el contorno. Si no, en el tramo donde menos alarga el recorrido (inserción más barata). El insertado queda seleccionado, así que los clics siguientes se encadenan en orden.
- **Al cambiar a Añadir o Contorno se quita la selección.** Si no, el primer círculo entraba detrás de un círculo antiguo y salían brazos cruzados.
- **Negativo (`N`)** y la casilla **«En el contorno»** están en el panel del círculo seleccionado. La casilla mete o saca el círculo sin borrarlo.
- Los círculos **fuera del contorno** se ven con trazo discontinuo, y los números de orden aparecen en Añadir y en Contorno.
- Cambiar la visibilidad de las capas **no ocupa el historial** de deshacer.

## 5. Capas: se exporta lo que se ve

- Casillas de **Capas visibles:** Rejilla, Círculos de construcción, Marcas + / − y Guías de edición (`G`).
- El SVG y el PNG incluyen **exactamente lo visible, salvo las guías**, que nunca se exportan.
- Capas del SVG: `fondo`, `rejilla`, `forma`, `circulos-construccion` (positivos en color papel, negativos en tinta) y `marcas` (+ en papel, − en tinta).
- Los archivos se exportan como `goma-y-clavos.svg` y `goma-y-clavos.png`.

## 6. Aleatorio (`R`)

El primer generador metía los círculos en un bloque fijo y centrado de pocas celdas, y con 24 columnas salían diminutos. El actual:

1. Elige una **zona al azar del papel**: entre el 45 y el 100 % del ancho útil y entre el 35 y el 100 % del alto, en cualquier posición.
2. Coloca **4–9 positivos** de tamaños variados, **medidos respecto a la página** (u = W/12) y no a la rejilla, con separación mínima.
3. Los ordena por ángulo alrededor del centroide.
4. Entre dos vecinos, a veces añade un **negativo empujado hacia el centro**, que abre una muesca.
5. Descarta los intentos sin negativos, con tangentes imposibles o con tangentes que se cruzan (`selfCross`).

## 7. Almacenamiento

- Clave `localStorage`: **`goma-y-clavos-v1`**.
- **No se leen composiciones de Mesa de tangentes.** Las dos webs comparten origen (`meegl.github.io`), así que el almacenamiento es común. Una versión leía la clave antigua y convertía cápsulas en una mancha; se quitó.
- Un contorno vacío se guarda vacío y no se reconstruye al recargar.

## 8. Composición de prueba: el póster

Rejilla de paso p = 1 celda, radio ≈ 0,445·p. Con el imán no se puede: ver §9. Coordenadas (columna, fila), en el orden del contorno:

```
(0,0)+ (1,0)+ (3,2)− (4,2)+ (2,3)− (4,4)+ (4,5)+ (3,5)−
(3,6)+ (1,5)− (0,6)+ (0,5)+ (1,4)− (1,3)− (0,3)+ (1,2)−
```

En A4 vertical con 12 columnas: x = 200 + 50·col, y = 250 + 50·fila, r = 22,25.

## 9. Limitaciones conocidas e ideas pendientes

- **El imán no deja hacer el póster.** Los radios van en cuartos de celda. Con círculos a una celda, r = 0,5 hace que los vecinos se toquen y la tangente cruzada es imposible. Idea: un **radio base global**, por ejemplo el 45 % de la celda, o pasos de radio más finos.
- **Insertar pulsando cerca de una tangente**, para meter un círculo en un tramo sin seleccionar antes su vecino.
- Crear círculos que nazcan **fuera** del contorno, por ejemplo con Alt + clic en Añadir.
- `selfCross` solo comprueba las rectas, no los arcos.
- Sin el contorno, un círculo negativo no tiene representación propia. Ahora mismo no aplica, porque siempre hay contorno o el círculo está fuera.
- En el repositorio de Mesa de tangentes queda una rama `circulos-negativos` antigua, que ya no se usa.

## 10. Cómo se trabaja en el proyecto

- **Un único `index.html`**, sin dependencias ni compilación. JavaScript ES5 (`var`, `function`), al estilo del resto del archivo. Interfaz y comentarios en español.
- Se prueba en local con un servidor estático; abrir el archivo directamente con `file://` no siempre funciona en el navegador integrado:

  ```bash
  python3 -m http.server 8765
  ```

- GitHub Pages publica la rama `main` desde la raíz.
- Commits en español, con una primera línea corta que describe el cambio.
