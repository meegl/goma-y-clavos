# Goma y clavos

Editor de formas: círculos positivos y negativos recorridos en orden por una goma elástica de tangentes. Exporta SVG por capas y PNG.

- Antes de cambiar el comportamiento, lee **[DECISIONES.md](DECISIONES.md)**: explica el modelo, las reglas de interacción y lo que se descartó (cápsulas, radio de empalme, Vista limpia, textos de esquina) y por qué. Si cambias algo de lo que describe, actualízalo.
- Todo vive en `index.html`, sin dependencias ni compilación. Hay tres bloques `<script>`: `TB` (geometría del contorno), `TGX` (exportador SVG) y la aplicación. Usa ES5, como el resto del archivo. La interfaz, los comentarios y los commits van en español.
- Para probar, levanta `python3 -m http.server 8765` y abre `http://localhost:8765`. Comprueba en el navegador cualquier cambio visible.
- Clave de `localStorage`: `goma-y-clavos-v1`. No leas claves de Mesa de tangentes: comparten origen en `meegl.github.io`.
- GitHub Pages publica `main` desde la raíz: https://meegl.github.io/goma-y-clavos/
