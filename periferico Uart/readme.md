# Periférico UART — Especificación e integración con Chain Mono (M5Stack)
## Protocolo de comunicaciòn uart
El protocolo UART (Transmisor Receptor Asíncrono Universal) se encarga de la transmisión y recepción de datos a través de sus puertos Tx y Rx. Funciona bajo el principio de comunicación serie, es decir, transmite los datos bit a bit a través de una sola línea.
La comunicación UART es una conexión física punto a punto entre dos dispositivos que cruza sus líneas de datos y comparte una referencia eléctrica:
• Tx (Transmisor): Línea que envía los datos. Se conecta al Rx del otro dispositivo.
• Rx (Receptor): Línea que recibe los datos. Se conecta al Tx del otro dispositivo.
• GND (Tierra): Línea común obligatoria para que ambos compartan la misma referencia de voltaje.

### ¿Cómo viaja la información?
En estado de reposo, el cable mantiene un voltaje alto. Cuando se inicia la comunicación, el transmisor genera cambios rápidos de voltaje (alto/bajo) que el receptor lee secuencialmente, bit por bit.
<img width="600" height="583" alt="Introduction-to-UART-Data-Transmission-Diagram-UART-Gets-Byte-from-Data-Bus-600x583" src="https://github.com/user-attachments/assets/e367fa60-4ea2-44ae-8fcf-55d29ca0ab2b" />
# Pantalla M5STACK
Chain Mono es un nodo de pantalla LED de la serie Chain de M5Stack, que incorpora una unidad de matriz de puntos LED monocromática de 8×8. Admite el control independiente de píxeles, la escritura de píxeles por lotes y la actualización rápida del búfer de pantalla completa. Integra generación de caracteres ASCII, desplazamiento de cadenas de texto, ajuste de brillo y rotación de pantalla en varios ángulos, lo que permite crear diversos efectos dinámicos de luz y animaciones de píxeles. Es ideal para creaciones de luces de píxeles, iluminación ambiental de escritorio, letreros luminosos creativos e iluminación indicadora para dispositivos inteligentes. Chain Mono funciona con un controlador principal STM32G031G8U6 y utiliza un protocolo de comunicación serie UART en cadena (daisy-chain). A través de dos interfaces de expansión HY2.0-4P, se puede ampliar con más dispositivos de la serie Chain para construir aplicaciones interactivas más completas.

<img width="1600" height="1040" alt="M5STACK" src="https://github.com/user-attachments/assets/406a6764-53d2-4efb-abb8-ee6831174e31" />

## Especificaciones tècnicas

| Especificación | Parámetro |
|---|---|
| MCU | STM32G031G8U6 |
| Alimentación de entrada | DC 5 V |
| Comunicación | UART 115200 bps @ 8N1 |
| Interfaz | 2 x HY2.0-4P |
| Color del LED | Blanco |
| Consumo en reposo | DC 5 V @ 8.13 mA |
| Consumo en operación | DC 5 V @ 22.29 mA (brillo máximo, todos encendidos) |
| Temperatura de operación | 0 ~ 40 °C |
| Tamaño del producto | 24.0 x 24.0 x 16.0 mm |
| Peso del producto | 6.2 g |

##  Uso del Chain Mono en el videojuego

El juego usa **4 módulos M5STACK** conectados en cadena como una sola pantalla LED.

### La pantalla

| Característica | Detalle |
|---|---|
| Resolución por módulo | 8 × 8 píxeles |
| Resolución total (4 módulos en fila) | **32 × 8 píxeles** |
| Color | Monocromático (blanco), cada LED solo está encendido o apagado |
| Escala de grises / color | No soporta |

>  El marcador debe ser corto.

###  Cómo se dibuja

Cada módulo tiene un **búfer de imagen de 8 bytes**: un byte por fila y un bit por LED.

```
Fila 0 → 0b00111100
Fila 1 → 0b01000010
Fila 2 → 0b10100101
...
Fila 7 → 0b00111100
```

Funciones disponibles:

- **Píxel individual:** enciende o apaga un LED concreto.
- **Escritura por lotes:** modifica varios píxeles en un solo comando.
- **Refresco completo:** envía el cuadro entero de una vez (el más adecuado para un juego: se arma el cuadro y se manda).
- **Texto ASCII integrado:** con desplazamiento de hasta 32 caracteres por nodo, útil para el marcador o "GAME OVER".
- **Brillo ajustable.**
- **Rotación** de 0°, 90°, 180° y 270°, para orientar la pantalla sin cambiar la lógica del juego.

###  Conexión en cadena (daisy-chain)

Cada módulo tiene dos conectores HY2.0-4P: **IN** (entrada) y **OUT** (salida).

```
[Controlador] ──► IN [Módulo 1] OUT ──► IN [Módulo 2] OUT ──► IN [Módulo 3] OUT ──► IN [Módulo 4]
```

- El controlador se conecta al **IN del primer módulo**.
- El orden de la cadena determina qué parte de la pantalla dibuja cada módulo.
- Los módulos se unen entre sí con el **Chain Bridge**, respetando la flecha triangular del sentido de la cadena.

###  Comunicación

| Parámetro | Valor |
|---|---|
| Protocolo | UART |
| Velocidad | 115200 bps, 8N1 |
| Velocidad máxima teórica | ~11,520 bytes/s |

Cada cuadro de un módulo ocupa 8 bytes más la cabecera del protocolo. Con 4 módulos hay que enviar 4 cuadros por refresco, y esto **limita los cuadros por segundo** del juego.

###  Alimentación

- Se alimenta a **5 V** a través del bus.
- Consumo máximo por módulo: ~22.29 mA.
- Consumo máximo de los 4 módulos: **~90 mA**, que cualquier controlador puede suministrar.

###  Funcionamiento interno

- Un **STM32G031G8U6** recibe los comandos y se encarga de encender los LEDs.
- Los 64 LEDs se manejan con solo **9 pines** mediante *charlieplexing*: el microcontrolador enciende los LEDs de uno en uno, tan rápido que parece que están todos encendidos a la vez.
- El controlador del juego **no necesita hacer ese refresco**, solo enviar el dibujo.

| Tamaño del paquete | 138.0 x 93.0 x 11.0 mm |
| Peso bruto | 13.2 g |
