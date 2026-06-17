# SUMO_JRG26

<p align="justify">
En este repositorio explico cómo funcionaba el algoritmo de sumo que utilizamos con mi equipo de robótica, formado por Carlos Ríos, Diego López, Martín Rincón y Sergio Toro, durante el <strong>ASTI Robotics Challenge X</strong>.
</p>

<p align="justify">
Este repositorio puede entenderse como un anexo del proyecto original, pensado para documentar con más detalle el funcionamiento del robot durante esta prueba.
</p>

## Introducción

<p align="justify">
El objetivo de esta prueba era detectar tres robots que se movían a gran velocidad dentro de una pista circular de un metro de diámetro y sacarlos del ring lo más rápido posible, evitando que nuestro propio robot se saliera.
</p>

## Hardware

<p align="justify">
Nuestro robot utiliza como cerebro un microcontrolador <strong>ESP32</strong>. Esto supone una limitación en algunos aspectos, pero también tiene ventajas importantes, especialmente por su sencillez, bajo coste y facilidad de integración.
</p>

<p align="justify">
Uno de los principales requisitos del proyecto fue diseñar un algoritmo lo más simple y robusto posible, capaz de ejecutarse periódicamente, reaccionar con rapidez y adaptarse a diferentes situaciones dentro del ring.
</p>

<p align="justify">
Para detectar a los robots contrincantes se utilizó un sensor láser de tiempo de vuelo <strong>VL53L1X</strong>. Para el movimiento de nuestro robot se emplearon motores DC con encoder, adquiridos en AliExpress.
</p>

## Software

<img src="./assets/ALGORITMO.png" align="right" width="270" alt="Esquema del algoritmo de sumo">

<p align="justify">
Antes de entrar en detalle en el algoritmo de sumo, decidimos que la mejor estrategia era desarrollar nuestras propias librerías: una para realizar el control de velocidad de los motores y otra para manejar el sensor láser mediante unas pocas funciones sencillas.
</p>

<p align="justify">
De esta forma, el código principal del robot podía centrarse en la máquina de estados y en alguna funcionalidad adicional que comentaremos más adelante.
</p>

<p align="justify">
Otra de las decisiones tomadas desde el inicio fue implementar un sistema sencillo de ejecución periódica de tareas. Para ello se utilizó la librería <code>Ticker.h</code>, que nos permitió organizar ciertas funciones de forma similar a un RTOS simplificado.
</p>

<p align="justify">
El algoritmo de sumo es sencillo y se divide en las siguientes fases:
</p>

1. El robot busca un objetivo girando sobre sí mismo.
2. Cuando detecta un robot contrario, inicia un pequeño algoritmo de apuntado.
3. Una vez orientado hacia el objetivo, ataca primero a alta velocidad para alcanzarlo rápidamente.
4. Después reduce ligeramente la velocidad para evitar salirse del círculo.
5. Al finalizar el ataque, retorna a la posición de origen y vuelve a iniciar el algoritmo.

<p align="justify">
En la imagen se muestra un ejemplo simplificado del comportamiento del robot durante la prueba de sumo.
</p>

<p align="justify">
Sobre el papel, no parecía necesario implementar un algoritmo de apuntado como el que se muestra en la imagen de la derecha. La idea inicial era sencilla: en cuanto el robot detectaba al enemigo, atacaba directamente. Sin embargo, en las pruebas vimos que este comportamiento no era suficiente. Si pasábamos directamente del estado de búsqueda al estado de ataque, nuestro robot tendía a pasar de largo, golpear de lado o no impactar correctamente contra el rival.
</p>

<p align="justify">
Para resolver este problema añadimos un estado intermedio muy simple entre la detección del enemigo y el ataque. La lógica utilizada fue la siguiente:
</p>

1. **Buscamos robot** → gira a la izquierda.
2. **Robot encontrado** → sigue girando a la izquierda.
3. **Robot perdido** → gira a la derecha despacio.
4. **Robot encontrado de nuevo** → ataca.

<p align="justify">
Con este algoritmo tan simple, que fuimos calibrando de forma práctica durante las pruebas, conseguimos que los robots enemigos fueran golpeados con el centro de nuestro robot. Esto evitaba que nuestro robot se pasara de largo o que impactara de lado, aumentando bastante la eficacia del ataque.
</p>

<br clear="right"/>
