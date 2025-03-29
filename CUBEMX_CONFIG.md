# Configuración del Proyecto con STM32CubeMX

En esta sección, utilizaremos la funcionalidad de STM32CubeMX (integrada en la extensión de VS Code) para configurar los periféricos necesarios de la placa Nucleo-L476RG.

## 1. Abrir la Configuración de CubeMX

1.  Asegúrate de tener el proyecto clonado abierto en VS Code.
2.  Busca el archivo con extensión `.ioc` en el explorador de archivos de VS Code (ej., `ProyectoIntro.ioc`).
3.  Haz clic derecho sobre el archivo `.ioc` y selecciona **"Open with STM32CubeMX"**. (Si no tienes un archivo `.ioc` base, puedes crearlo desde la paleta de comandos de VS Code: `Ctrl+Shift+P`, busca `STM32: New Project`, selecciona la placa NUCLEO-L476RG).
4.  Esto abrirá la interfaz gráfica de STM32CubeMX dentro de VS Code o en una ventana separada. Si pregunta sobre inicializar periféricos por defecto, puedes seleccionar "Yes".

## 2. Configuración de Periféricos

Sigue estos pasos en la interfaz de STM32CubeMX:

### 2.1 Configuración del Reloj (Clock Configuration)

1.  Ve a la pestaña **"Clock Configuration"**.
2.  Busca el cuadro **"HCLK (MHz)"** e introduce `80`. STM32CubeMX intentará ajustar automáticamente las fuentes y divisores para alcanzar los 80 MHz. Asegúrate de que no haya errores marcados en rojo.
3.  Verifica que la fuente de reloj para `USART2` (conectado al bus APB1) sea `PCLK1`.

    *(Puedes incluir una imagen similar a `stm32cubemx_clock.png` aquí si lo deseas)*

### 2.2 Configuración de Pines (Pinout & Configuration)

Ve a la pestaña **"Pinout & Configuration"**.

1.  **LED Integrado (LD2):**
    *   Localiza el pin **PA5** en el diagrama del chip.
    *   Haz clic sobre él y selecciona **`GPIO_Output`**.
    *   En el panel izquierdo (debajo de `System Core > GPIO`), haz clic en PA5 y, en la configuración que aparece a la derecha, puedes ponerle una etiqueta de usuario (User Label) como `LD2`.

2.  **LED Externo (Simulador de Cerradura):**
    *   Vamos a usar otro pin GPIO. Por ejemplo, **PB5**.
    *   Haz clic en **PB5** y selecciona **`GPIO_Output`**.
    *   Asígnale una etiqueta de usuario como `LED_EXT`.
    *   *Nota:* Deberás conectar físicamente un LED (con su resistencia limitadora) entre el pin PB5 y GND.

3.  **Botón Integrado (B1):**
    *   Localiza el pin **PC13**. Ya debería estar configurado por defecto como `GPIO_EXTI13` (External Interrupt).
    *   Verifica que su configuración sea:
        *   GPIO mode: `External Interrupt Mode with Falling edge trigger detection`.
        *   GPIO Pull-up/Pull-down: `No pull-up and no pull-down` (la placa Nucleo ya tiene un pull-up externo para este botón).
    *   Asígnale una etiqueta de usuario como `B1`.

    *(Puedes incluir una imagen similar a `stm32cubemx_gpio.png` aquí si lo deseas)*

4.  **Comunicación UART (USART2):**
    *   En el panel izquierdo, expande **"Connectivity"** y selecciona **`USART2`**.
    *   Mode: Selecciona **`Asynchronous`**.
    *   Configuration -> Parameter Settings:
        *   Baud Rate: `115200` Bits/s
        *   Word Length: `8 Bits` (con paridad incluida si se usa)
        *   Parity: `None`
        *   Stop Bits: `1`
    *   Configuration -> NVIC Settings:
        *   Marca la casilla **"Enabled"** para `USART2 global interrupt`. Esto permitirá que el microcontrolador reaccione cuando lleguen datos por UART sin detener el programa principal.

    *(Puedes incluir una imagen similar a `stm32cubemx_uart.png` aquí si lo deseas)*

5.  **Configuración de Interrupciones Externas (NVIC):**
    *   En el panel izquierdo, ve a **`System Core > NVIC`**.
    *   Busca la línea **"EXTI line[15:10] interrupts"** (PC13 corresponde a la línea EXTI 13, que está en este grupo).
    *   Asegúrate de que la casilla **"Enabled"** esté marcada. Esto permite que la interrupción del botón funcione.

    *(Puedes incluir una imagen similar a `stm32cubemx_nvic.png` aquí si lo deseas)*

## 3. Generación del Código

1.  Ve a la pestaña **"Project Manager"**.
2.  En la sub-pestaña "Project":
    *   Dale un nombre al proyecto (ej. `Intro_EC`).
    *   Asegúrate de que la `Toolchain / IDE` esté configurada como **`STM32CubeIDE`** o **`Makefile`**. *Importante:* Si usas la extensión de VS Code, a menudo funciona mejor generando para `STM32CubeIDE` ya que comparte el formato, o directamente usando la generación CMake si la extensión lo soporta explícitamente para tu versión. Consulta la documentación de la extensión si tienes dudas. Para CMake, puede que necesites configurar `Toolchain/IDE` a `CMake`.
3.  En la sub-pestaña "Code Generator":
    *   Asegúrate de que esté seleccionada la opción **"Generate peripheral initialization as a pair of '.c/.h' files per peripheral"**.
    *   Marca **"Keep User Code when re-generating"** (¡Muy importante!).
4.  Haz clic en el botón **"Generate Code"** (usualmente un icono de engranaje o un botón con ese texto).
5.  STM32CubeMX generará/actualizará los archivos de configuración y el código de inicialización HAL en tu proyecto dentro de las carpetas `Core` y `Drivers`.

¡La configuración del hardware está lista! Ahora vamos a escribir la lógica de la aplicación.

**Siguiente Paso:** [Implementación del Código (CODE_IMPLEMENTATION.md)](CODE_IMPLEMENTATION.md)
