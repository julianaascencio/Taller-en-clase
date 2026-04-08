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

<img width="729" height="247" alt="image" src="https://github.com/user-attachments/assets/1a771175-fd21-4347-b120-c8538be79a67" />


Esta fase permitió evidenciar cómo el protocolo TCP garantiza la entrega confiable de datos durante la descarga de archivos desde internet.

## PASO 3: Captura de tráfico UDP (simulación de video)
### Iniciar captura UDP

En esta fase del laboratorio se realizó la captura del tráfico de red asociado a una simulación de transmisión de video en tiempo real, utilizando el protocolo UDP. A diferencia de la fase anterior, en la que se analizó la descarga de un archivo mediante TCP, en este caso se buscó representar una comunicación rápida y continua, donde la prioridad no es la fiabilidad absoluta sino la baja latencia.

Para ello, primero se inició una captura de paquetes con la herramienta tcpdump, enfocada exclusivamente en el tráfico UDP sobre el puerto 12345. El comando utilizado fue el siguiente:

```bash
!sudo tcpdump -i eth0 -s 1500 -w video_udp.pcap udp and port 12345 &
print("Captura UDP iniciada")
```
- tcpdump captura tráfico de red
- i eth0 usa la interfaz principal
- s 1500 captura suficiente contenido
- w video_udp.pcap guarda el tráfico en archivo
- udp and port 12345 filtra solamente tráfico UDP en ese puerto
- & hace que quede corriendo en segundo plano
- 
<img width="705" height="180" alt="image" src="https://github.com/user-attachments/assets/3b172a59-f27d-45ff-99ce-a6f41d0d1046" />

### Crear el tráfico UDP (simular video)
Este comando permitió registrar únicamente los paquetes UDP intercambiados en el puerto especificado, almacenándolos en un archivo .pcap llamado video_udp.pcap, el cual será analizado posteriormente en Wireshark.

Una vez iniciada la captura, se ejecutó un script en Python que simuló una transmisión de video mediante el envío de 100 paquetes UDP consecutivos. Cada paquete contenía datos aleatorios que representaban un fotograma o fragmento de video. El código utilizado fue:

```bash
import socket
import time
import random

UDP_IP = "127.0.0.1"
UDP_PORT = 12345
FRAME_SIZE = 1024

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

print("Iniciando transmisión de video simulada por UDP")

for i in range(100):
    message = f"Frame_{i}_Data_" + ''.join(random.choices('ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789', k=FRAME_SIZE))
    sock.sendto(message.encode(), (UDP_IP, UDP_PORT))
    time.sleep(0.05)

print("Transmisión finalizada")
```
<img width="729" height="566" alt="image" src="https://github.com/user-attachments/assets/107d9929-c039-4702-9104-5d833990f7fd" />


### Detener captura UDP
En este script, se utilizó la librería socket para crear un canal de comunicación UDP, configurando como destino la dirección local 127.0.0.1 y el puerto 12345. Posteriormente, se enviaron múltiples paquetes con un intervalo corto de tiempo entre ellos, simulando así un flujo continuo de datos similar al de una transmisión en tiempo real.

Finalizada la simulación, se detuvo la captura de paquetes con el siguiente comando:

```bash
!sudo pkill tcpdump
print("Captura UDP detenida")
```
<img width="754" height="154" alt="image" src="https://github.com/user-attachments/assets/fc75180c-a4f7-4ad0-8293-e0e449d11f1a" />

### Descargar archivo UDP
Finalmente, el archivo video_udp.pcap fue descargado para su análisis en Wireshark.

Esta fase permitió evidenciar el comportamiento del protocolo UDP, el cual se caracteriza por su rapidez y simplicidad, ya que no requiere establecer una conexión previa ni garantiza la retransmisión de paquetes perdidos. Estas características lo hacen adecuado para aplicaciones en tiempo real, como transmisión de audio o video, donde es preferible mantener la fluidez del servicio antes que esperar la recuperación de datos perdidos.

```bash
from google.colab import files
files.download("video_udp.pcap")
```
<img width="894" height="735" alt="image" src="https://github.com/user-attachments/assets/8f63694e-eab7-4ae3-a690-5c32b7e67311" />

A través de esta fase se pudo observar que UDP resulta apropiado para aplicaciones en tiempo real, ya que reduce la latencia al no depender de mecanismos de confirmación o retransmisión.

## Paso 4: Análisis en Wireshark
## Parte 1: Análisis del archivo TCP
### Ver inicio de conexión (Handshake)
Iniciamos analizando el primer archivo "descarga_tcp.pcap", abrimos el archivo en Wireshark.
Para analizar el inicio de la comunicación TCP se utilizó el filtro:

tcp.flags.syn == 1

Este filtro permite identificar los paquetes SYN, los cuales son utilizados para iniciar una conexión TCP. A partir de estos paquetes se puede observar el establecimiento del three-way handshake, mecanismo mediante el cual se crea una conexión confiable entre cliente y servidor.

<img width="1025" height="1005" alt="image" src="https://github.com/user-attachments/assets/355845f7-1cc4-4a97-bdba-339b7f29bce5" />

Durante el análisis del archivo descarga_tcp.pcap se observó tráfico correspondiente al protocolo TCP y HTTP entre las direcciones IP internas del entorno de Google Colab.

En la captura se identifican solicitudes HTTP tipo GET, lo cual indica que el cliente está solicitando recursos a un servidor, en este caso asociados a la descarga del modelo YOLOv8. Este comportamiento evidencia el intercambio de datos necesario para completar la descarga.

Las direcciones IP observadas (172.28.0.12 y 172.28.0.1) corresponden a direcciones internas del entorno de ejecución, lo que confirma que el tráfico se maneja dentro de la infraestructura de Colab.

Aunque no se observaron paquetes explícitos en el puerto 443, sí se evidenció tráfico TCP activo, lo cual permite analizar el comportamiento del protocolo durante la transferencia de datos.

Filtro: tcp.analysis.retransmission

<img width="1014" height="827" alt="image" src="https://github.com/user-attachments/assets/80954171-16cd-4692-986a-1b91f845041c" />

En la imagen se observa el resultado del filtro tcp.analysis.retransmission aplicado sobre el archivo descarga_tcp.pcap.

Este filtro permite identificar los paquetes que han sido retransmitidos durante la comunicación TCP. Las retransmisiones ocurren cuando un paquete no llega correctamente al destino o no es confirmado, por lo que el protocolo TCP lo envía nuevamente para garantizar la integridad de la información.

En este caso, se evidencian varios paquetes marcados como "TCP Retransmission", lo que indica que durante la descarga del modelo se presentaron pequeñas pérdidas o retrasos en la transmisión de datos. Sin embargo, gracias a los mecanismos de control de TCP, estos paquetes fueron reenviados correctamente.

Este comportamiento demuestra una de las principales características del protocolo TCP: su capacidad para asegurar la entrega confiable de los datos, incluso en presencia de errores o congestión en la red.
TCP Stream

<img width="948" height="971" alt="image" src="https://github.com/user-attachments/assets/c04317d0-4666-42f5-b90c-e690902e9317" />

Este mecanismo es fundamental en la descarga de archivos, ya que permite recuperar información perdida. No obstante, en aplicaciones en tiempo real como video, las retransmisiones pueden generar latencia y afectar la fluidez de la transmisión.

## Parte 2: Análisis del archivo UDP
Abrimos el archivo video_udp.pcap en wireshark para poder analizarlo.

i bby<img width="550" height="288" alt="image" src="https://github.com/user-attachments/assets/1ad1267f-2bc0-4164-95d9-b68ac7c857fb" />

### Aplicar filtro
udp.port == 12345
<img width="579" height="309" alt="image" src="https://github.com/user-attachments/assets/35e6ebfa-3ec9-48b6-bf4e-6b5ed830b224" />
En la imagen se presenta el análisis del tráfico UDP capturado durante la simulación de transmisión de video, utilizando el filtro udp.port == 12345 en Wireshark.

Se observa un conjunto de paquetes enviados desde la dirección IP 172.28.0.12 hacia 8.8.8.8, todos utilizando el puerto 12345, lo cual corresponde a la configuración realizada en el script de simulación. Los paquetes presentan un tamaño constante (aproximadamente 1026 bytes), lo que indica un flujo uniforme de datos.

Adicionalmente, se evidencia que los paquetes son enviados de forma continua en intervalos muy cortos de tiempo, lo que simula un flujo de información similar al de una transmisión de video en tiempo real.

En la sección inferior de la imagen se muestra el detalle de un paquete UDP, donde se pueden observar campos como la dirección IP de origen y destino, el puerto de origen, el puerto de destino y la longitud del paquete. A diferencia del protocolo TCP, no se observan mecanismos de establecimiento de conexión ni retransmisión de paquetes.

Este comportamiento confirma que el protocolo UDP está orientado a la rapidez y baja latencia, permitiendo el envío continuo de datos sin verificar su correcta recepción, lo cual lo hace ideal para aplicaciones en tiempo real como la transmisión de video.

La ausencia de retransmisiones y confirmaciones en UDP reduce el tiempo de entrega de los datos, pero implica que algunos paquetes pueden perderse sin ser recuperados.
Este comportamiento contrasta con el protocolo TCP, donde se prioriza la confiabilidad sobre la velocidad.


## Resultados

Durante el desarrollo del laboratorio se logró capturar tráfico tanto TCP como UDP, evidenciando diferencias importantes entre ambos protocolos.

En el análisis TCP se observó el establecimiento de conexiones, así como retransmisiones de paquetes, lo cual confirma su enfoque en la confiabilidad.

En el análisis UDP se evidenció un flujo continuo de paquetes sin control de errores ni retransmisiones, lo que demuestra su enfoque en la velocidad y baja latencia.

## Conclusiones

El desarrollo de este laboratorio permitió comprender de manera práctica el funcionamiento de los protocolos TCP y UDP en diferentes escenarios.

Se evidenció que TCP es ideal para la transferencia de archivos, ya que garantiza la entrega completa y ordenada de los datos mediante mecanismos como retransmisión y control de errores.

Por otro lado, UDP demostró ser más eficiente para aplicaciones en tiempo real, como la transmisión de video, debido a su baja latencia y simplicidad, aunque no garantiza la entrega de los paquetes.

Adicionalmente, se identificó que en entornos virtualizados como Google Colab pueden existir limitaciones en la captura de tráfico, lo cual requirió ajustes en la configuración para lograr resultados adecuados.

En conclusión, la elección entre TCP y UDP depende de las necesidades de la aplicación, priorizando confiabilidad o velocidad según el caso.
