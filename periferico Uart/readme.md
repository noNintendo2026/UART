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
