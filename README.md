# Taller-en-clase

## Integrantes
Lesly Jualiana Ascencio Peréz

## Introducción
Su misión es analizar el "rastro" digital que deja el potente modelo de visión por computadora YOLOv8 mientras opera, y comparar el comportamiento de sus dos protocolos de red principales: TCP y UDP.

## Objetivo
Entender, analizar y contrastar el tráfico de red generado por YOLOv8 durante dos tareas clave: la descarga inicial de sus componentes (usando TCP) y la inferencia en tiempo real desde una cámara web (usando UDP)

## Paso 1: Preparación del entorno
Para el desarrollo del laboratorio se utilizó la plataforma Google Colab, la cual proporciona un entorno de ejecución basado en la nube con sistema operativo Linux. Esta herramienta permite ejecutar código en Python sin necesidad de instalar software adicional en el equipo local.

Inicialmente, se configuró el entorno de ejecución con aceleración por hardware (GPU), con el fin de optimizar el desempeño del modelo YOLOv8. Sin embargo, es importante mencionar que para este ejercicio el uso de GPU no es obligatorio, ya que únicamente se realiza la descarga del modelo y no su entrenamiento.

Posteriormente, se procedió a instalar las herramientas necesarias para el desarrollo de la práctica. En primer lugar, se instaló la librería Ultralytics, la cual permite trabajar con el modelo de visión por computadora YOLOv8. En segundo lugar, se instaló la herramienta tcpdump, utilizada para capturar y analizar el tráfico de red generado durante la ejecución del laboratorio.

El código empleado para la instalación fue el siguiente:

``` bash
!pip install ultralytics -q
!apt-get update -qq && apt-get install -y -qq tcpdump

print("Entorno listo")
```
<img width="380" height="82" alt="image" src="https://github.com/user-attachments/assets/6fe648fe-2499-41f7-af77-cc638f948657" />

La instrucción pip install ultralytics permite descargar e instalar la librería necesaria para cargar y utilizar el modelo YOLOv8. Por su parte, el comando apt-get install tcpdump instala una herramienta de análisis de red que permite capturar paquetes de datos en tiempo real, los cuales serán almacenados en archivos con extensión .pcap para su posterior análisis en Wireshark.

Finalmente, se verificó que la instalación se realizara correctamente mediante un mensaje de confirmación en la consola tal y como se muestra en la imagen.
<img width="455" height="122" alt="image" src="https://github.com/user-attachments/assets/a38a24ff-9ff6-43db-9861-5e5fe7a12c40" />

Este paso es fundamental, ya que permite preparar el entorno de trabajo y asegurar la correcta ejecución de las fases posteriores del laboratorio.

## Paso 2: Captura de tráfico TCP (descarga del modelo)
### Iniciar captura de tráfico TCP
En esta fase del laboratorio se realizó la captura del tráfico de red generado durante la descarga del modelo YOLOv8. Este proceso se llevó a cabo utilizando la herramienta tcpdump, la cual permite monitorear y registrar los paquetes que circulan por la red en tiempo real.

Inicialmente, se ejecutó el comando de captura sobre la interfaz de red principal (eth0), configurando un tamaño de captura adecuado y definiendo como salida un archivo en formato .pcap, el cual almacena la información del tráfico para su posterior análisis.

El código utilizado fue el siguiente:

```bash
!sudo tcpdump -i eth0 -s 1500 -w descarga_tcp.pcap &
print("Captura TCP iniciada")
```

- tcpdump → herramienta que captura paquetes de red
- i eth0 → usa la red principal de Colab
- s 1500 → captura suficiente información de cada paquete
- w descarga_tcp.pcap → guarda todo en un archivo
- & → lo deja corriendo en segundo plano
  
<img width="713" height="252" alt="image" src="https://github.com/user-attachments/assets/5c252a0e-5369-4467-988f-3d72c671f48d" />

### Generar tráfico (descargar YOLO)

Una vez iniciada la captura, se procedió a generar tráfico de red mediante la descarga del modelo preentrenado yolov8n.pt, utilizando la librería Ultralytics. Este proceso activa una comunicación con servidores externos a través del protocolo TCP, ya que se requiere una transferencia de datos confiable, ordenada y sin pérdidas.

```bash
from ultralytics import YOLO

model = YOLO("yolov8n.pt")
print("Modelo descargado")
```

- Se descarga el archivo yolov8n.pt
- Ese archivo viene de internet
- Usa TCP (protocolo confiable)
Como tcpdump está activo, todo ese tráfico queda grabado

<img width="718" height="309" alt="image" src="https://github.com/user-attachments/assets/1e532373-8d76-4cff-ac6c-0a27c34b8636" />

### Detener captura
Durante este proceso, tcpdump registró todos los paquetes intercambiados, incluyendo el establecimiento de la conexión, la transferencia de datos y la finalización de la comunicación.

Posteriormente, se detuvo la captura utilizando el comando correspondiente, lo que permitió cerrar correctamente el archivo de salida:

```bash
!sudo pkill tcpdump
print("Captura TCP detenida")
```
<img width="410" height="131" alt="image" src="https://github.com/user-attachments/assets/c02fda99-dd89-46bd-ac57-a9d23b357ece" />

### Descargar archivo
Finalmente, el archivo generado fue descargado para su análisis en la herramienta Wireshark, donde se examinará el comportamiento del protocolo TCP.
Este procedimiento corresponde a la fase de análisis de descarga descrita en el laboratorio, en la cual se busca observar el funcionamiento de un protocolo confiable orientado a conexión.

<img width="888" height="840" alt="image" src="https://github.com/user-attachments/assets/fa642977-8b60-4247-9d43-54e751fb8872" />

Esta fase permitió evidenciar cómo el protocolo TCP garantiza la entrega confiable de datos durante la descarga de archivos desde internet.

