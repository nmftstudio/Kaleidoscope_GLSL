# NMFT Kaleidoscope — v3.0

Caleidoscopio generativo en WebGL puro (sin librerías externas de shaders), con reacción al giroscopio, al tacto y a la cámara del dispositivo. Hecho por [NMFT STUDIO](https://nmft.ar).

Un solo archivo HTML autocontenido: `kaleidoscope-nmft-v2.html`. Se abre directo en el navegador, no necesita build ni servidor especial (salvo HTTPS para usar la cámara y los sensores de orientación).

---

## Características

### Patrones (7)
Se ciclan con el botón **Patrón**:

| Patrón | Descripción |
|---|---|
| Ondas | Anillos concéntricos suaves |
| Cuadrícula | Celdas en damero con líneas divisorias |
| Pétalos | Forma floral con contorno |
| Espiral | Bandas curvas separadas por líneas finas |
| Líquido | Superficie fluida con reflejos ondulantes |
| Humo | Capas que se elevan y difuminan |
| Hielo | Facetas cristalinas tipo Voronoi con grietas |

### Paletas (12)
Se ciclan con el botón **Paleta**: Nebulosa, Selva, Lava, Violeta, Océano, Fuego, Bosque, Neón, Algodón de azúcar, Desierto, Ácido, Medianoche.

Cada paleta define 3 colores principales + 1 color de línea propio (no negro fijo). Al cambiar de paleta, los colores se **interpolan suavemente** frame a frame en vez de saltar de golpe — un mix real entre combinaciones.

### Cámara (interacción con la realidad)
Botón **Cámara**: pide permiso y activa el video. Una fracción de lo que capta:
1. **Deforma la geometría** de los patrones (afecta la forma, no solo el color).
2. **Tiñe el color final** con un tritono basado en la luminancia de la paleta activa (fusión tipo *screen blend*, no reemplazo).

Controles asociados:
- Slider **Mezcla**: cuánto pesa la cámara en la fusión (0–100%).
- Botón **Girar cámara**: alterna entre cámara trasera y frontal.

### Sensores de movimiento
Botón **Activar Sensores** (pide permiso en iOS). El giroscopio controla:
- Paneo del centro del patrón.
- Zoom y velocidad de rotación (más inclinación = más intensidad).
- Un destello blanco cuando la inclinación es fuerte (o la cámara capta mucha luz).

El centro del caleidoscopio también tiene una **deriva autónoma** constante (curva de Lissajous), así nunca queda estático aunque no haya interacción.

### Controles adicionales
- Slider **Segmentos**: 2 a 16, cambia la simetría radial en vivo.
- Botón **Guardar PNG**: exporta el frame actual.
- Botón **Pantalla completa**: entra en modo inmersivo — oculta toda la UI y deja solo un botón hamburguesa abajo a la izquierda para volver a mostrarla. También se puede hacer doble tap/doble click sobre el dibujo.
- El título se desvanece solo a los 5 segundos de abierta la app.
- Mouse / arrastre táctil: mueve el centro del patrón (funciona en desktop y como fallback en dispositivos sin giroscopio).

---

## Requisitos

- Navegador con soporte WebGL (todos los modernos).
- **HTTPS obligatorio** para pedir permisos de cámara (`getUserMedia`) y de orientación (`DeviceOrientationEvent.requestPermission`, requerido en iOS 13+).
- En desktop, sin cámara trasera, el navegador usa la que tengas disponible.

## Uso

1. Subir `kaleidoscope-nmft-v2.html` a hosting con HTTPS (por ejemplo dentro de nmft.ar).
2. Abrir desde el celular o la compu.
3. Tocar **Activar Sensores** y/o **Cámara** para sumar las capas de interacción (opcionales — sin ellas, el modo generativo funciona igual).

## Stack técnico

- WebGL 1 (fragment + vertex shader propios, sin dependencias de shaders externas).
- [GSAP](https://gsap.com/) (CDN) solo para las animaciones de aparición/desvanecido de la UI.
- Sin frameworks, sin build step, sin backend.

## Estructura del shader

El flujo dentro del fragment shader, en orden:

1. Coordenadas normalizadas centradas en pantalla.
2. Deriva autónoma + paneo por mouse/gyro.
3. Reflejo por segmentos (simetría del caleidoscopio) + rotación + zoom, todo modulado por el tilt del giroscopio.
4. Si la cámara está activa: sample único de la textura de video → deforma la geometría (`st`) antes de dibujar el patrón.
5. Selección de patrón (`u_patternType`) y color según la paleta activa.
6. Si la cámara está activa: fusión de color (tritono por luminancia + screen blend) reutilizando el mismo sample.
7. Destello final según inclinación y/o luminancia de cámara.

## Roadmap / ideas futuras

- Grabación de video corto (no solo captura PNG).
- Modo "auto": ciclar patrones y paletas solo, sin input.
- Más paletas mezcladas dinámicamente (interpolación entre 2 paletas elegidas, no solo transición secuencial).
- Audio-reactividad (micrófono) como otra fuente de "realidad" además de la cámara.

---

© NMFT STUDIO — [nmft.ar](https://nmft.ar)
