# LABORATORIO DE ATAQUES DE CAPA 2: CDP DoS

[cite_start]**Autor:** Sael Germán Garcia [cite: 100]  
[cite_start]**Matrícula:** 2025-0725 [cite: 102]  
[cite_start]**Asignatura:** Seguridad de Redes [cite: 106]  
[cite_start]**Profesor:** Jonathan Rondon [cite: 104]  

---

## 🎥 Evidencia Operacional (Video Demostración)
La demostración práctica de este ataque, la comprobación de saturación en los equipos Cisco y la aplicación de las contramedidas, se encuentran documentadas en la siguiente lista de reproducción:

▶️ **[Ver Playlist del Laboratorio en YouTube](https://youtube.com/playlist?list=PLV_dKVnYXf6dpmk3j8uXPHAZdbrkCQGAY&si=d9Hr6pnByXof9LZF)**

---

## 1. Objetivo del Laboratorio
[cite_start]El objetivo primordial de este laboratorio de infraestructura y seguridad de redes consiste en evaluar de manera práctica la resiliencia de las plataformas de conmutación (Switches) frente a vectores de ataque dirigidos a protocolos de control de la Capa 2 del modelo OSI[cite: 109]. [cite_start]Específicamente, se analiza el comportamiento del protocolo Cisco Discovery Protocol (CDP) bajo un escenario de denegación de servicio (DoS) por inundación (Flooding)[cite: 110].

## 2. Topología y Segmentación de Red
[cite_start]La infraestructura simulada se ha diseñado utilizando un esquema de direccionamiento IP basado en la matrícula del estudiante[cite: 128]. 

La red está segmentada de la siguiente manera:
* [cite_start]**VLAN 10 (Usuarios):** 10.7.25.0/24 - Asignada a usuarios finales legítimos (VPC1)[cite: 138].
* [cite_start]**VLAN 20 (Servidores):** 10.7.20.0/24 - Destinada a la zona de servidores locales (VPC2)[cite: 138].
* [cite_start]**VLAN 99 (Atacante):** 10.7.99.0/24 - VLAN nativa y de gestión donde reside la máquina atacante Ubuntu Linux[cite: 138].

## 3. Parámetros y Funcionamiento del Script (`cdp_dos.py`)
[cite_start]El script desarrollado en Python utiliza la biblioteca de manipulación de paquetes `scapy` para generar y transmitir de forma masiva tramas de anuncios de CDP totalmente sintetizadas mediante estructuras binarias de bajo nivel[cite: 144]. 

### Requisitos del Sistema
* [cite_start]**Sistema Operativo:** Linux (Ubuntu LTS recomendado)[cite: 157].
* [cite_start]**Privilegios:** Acceso Administrativo (Sudo/Root) indispensable para interactuar con sockets crudos a nivel de enlace de datos[cite: 158].
* [cite_start]**Intérprete y Librerías:** Python 3.x, Scapy, y los módulos core `struct`, `random` y `sys`[cite: 159, 160, 161].

### Desglose de Funciones Principales
* [cite_start]`random_mac()`: Genera una dirección MAC pseudoaleatoria en formato hexadecimal, aplicando una máscara de bits para asegurar que sea de tipo Unicast[cite: 165, 166].
* [cite_start]`cdp_checksum(data)`: Implementa manualmente el algoritmo oficial de Checksum de Internet (RFC 1071) para garantizar que el Switch procese el paquete como válido[cite: 167, 169].
* [cite_start]`build_cdp_packet(src_mac)`: Utiliza el módulo `struct.pack` para compilar los bloques TLV requeridos por el protocolo Cisco (Device ID, versión de IOS, VLAN nativa, etc.) encapsulando la carga en tramas IEEE 802.3 LLC/SNAP destinadas a la dirección Multicast de CDP[cite: 170].

### Sintaxis de Ejecución
```bash
sudo python3 cdp_dos.py [paquetes_a_enviar]
