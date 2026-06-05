# 🛡️ Laboratorio de Ataques de Capa 2 (L2 Security)

**Autor:** Sael Germán Garcia  
**Matrícula:** 2025-0725  
**Asignatura:** Seguridad de Redes  

Este repositorio contiene una suite de scripts desarrollados en Python utilizando la librería `scapy`. El objetivo principal es demostrar empíricamente las vulnerabilidades inherentes a los protocolos de control de la Capa de Enlace de Datos (Capa 2 del modelo OSI) y documentar los mecanismos de mitigación (Hardening) en infraestructuras Cisco IOS.

---

## 🎥 Evidencia Operacional (Video Demostraciones)

Todos los ataques descritos en este repositorio, junto con su respectiva prueba de concepto (PoC), verificación de impacto en los equipos Cisco y aplicación de contramedidas, se encuentran documentados en la siguiente lista de reproducción:

▶️ **[Ver Playlist Completa del Laboratorio en YouTube](https://youtube.com/playlist?list=PLV_dKVnYXf6dpmk3j8uXPHAZdbrkCQGAY&si=d9Hr6pnByXof9LZF)**

---

## 🏗️ Topología Unificada del Entorno

Los scripts fueron diseñados para operar sobre un entorno virtualizado estructurado de la siguiente manera:
* **VLAN 10 (Usuarios):** Segmento de pruebas de usuarios legítimos (10.7.25.0/24).
* **VLAN 20 (Servidores):** Segmento de servicios locales (10.7.20.0/24).
* **VLAN 99 (Gestión):** VLAN nativa y de administración.
* **Nodo Atacante:** Máquina Linux (Ubuntu) equipada con Python 3 y Scapy.

---

## 💻 Suite de Scripts de Ataque

### 1. CDP DoS Attack (`cdp_dos.py`)
Inunda la tabla de memoria del protocolo Cisco Discovery Protocol (CDP) mediante la inyección masiva de paquetes TLV sintetizados con direcciones MAC y nombres de dispositivos falsificados.
* **Ejecución:** `sudo python3 cdp_dos.py 1000`
* **Mitigación:** `no cdp run` (Global) o `no cdp enable` (Por interfaz).

### 2. ARP Man-in-the-Middle (`arp_mitm.py`)
Ejecuta un envenenamiento de las cachés ARP (ARP Spoofing) entre un host víctima y su Gateway predeterminado, logrando interceptar el flujo de tráfico de red a través del reenvío IP local (IP Forwarding).
* **Ejecución:** `sudo python3 arp_mitm.py <IP_Victima> <IP_Gateway>`
* **Mitigación:** Implementación de Dynamic ARP Inspection (DAI).

### 3. DHCP Spoofing (`dhcp_spoofing.py`)
Despliega un servidor DHCP malicioso (Rogue Server) que escucha en múltiples subinterfaces virtuales (VLAN Trunking). Intercepta mensajes `DHCP Discover` y responde agresivamente con parámetros de red alterados, estableciendo al atacante como Default Gateway y servidor DNS.
* **Ejecución:** `sudo python3 dhcp_spoofing.py`
* **Mitigación:** Configuración de DHCP Snooping y declaración de puertos confiables (Trusted Ports).

### 4. DHCP Starvation (`dhcp_starvation.py`)
Ataque de denegación de servicio (DoS) que agota la totalidad del bloque de direcciones IP disponibles en el servidor DHCP legítimo. Utiliza subprocesamiento (Multi-threading) para enviar ráfagas de solicitudes con direcciones MAC de origen pseudoaleatorias.
* **Ejecución:** `sudo python3 dhcp_starvation.py 2000`
* **Mitigación:** Implementación de Port Security en las interfaces de acceso.

### 5. MAC Flooding (`mac_flooding.py`)
Inunda la memoria de contenido direccionable (CAM Table) del Switch inyectando miles de tramas de Capa 2 inválidas en cuestión de segundos. Fuerza al equipo a entrar en un estado de "Fail-Open", comportándose como un Hub pasivo y permitiendo el sniffing de tráfico Inter-VLAN.
* **Ejecución:** `sudo python3 mac_flooding.py 5000`
* **Mitigación:** `switchport port-security maximum <valor>` y configuración de acciones de violación restrictivas.

### 6. STP Claim Root Attack (`stp_root.py`)
Secuestra la jerarquía topológica del Spanning Tree Protocol (STP). El script inyecta BPDUs de Configuración falsificados con una prioridad absoluta de puente igual a cero (0x0000), obligando a la red a converger y establecer a la máquina atacante como el nuevo Root Bridge.
* **Ejecución:** `sudo python3 stp_root.py 300`
* **Mitigación:** Implementación conjunta de características de aseguramiento: `spanning-tree guard root` y `spanning-tree bpduguard enable`.

---

## ⚙️ Requisitos del Sistema
Para replicar estos scripts en un entorno de laboratorio controlado:
* Entorno virtualizado (EVE-NG, PNETLab, o GNS3).
* Sistema Operativo Linux (Ubuntu LTS recomendado).
* Permisos de superusuario (root) para manipulación de Raw Sockets.
* Instalación de dependencias: `sudo apt update && sudo apt install -y python3-scapy python3-pip net-tools`

> ⚠️ **Aviso de Responsabilidad:** El contenido de este repositorio ha sido desarrollado con fines única y estrictamente académicos y educativos. La ejecución de estas herramientas fuera de un entorno de laboratorio aislado, sin el consentimiento explícito de los administradores de la red, representa una violación a las políticas de seguridad informática.

---

## 📚 Referencias Bibliográficas y Recursos

1. **Cisco Systems.** (n.d.). *Cisco Discovery Protocol Configuration Guide*. Documentación oficial de Cisco IOS.
2. **Scapy Project.** (2024). *Scapy: Interactive packet manipulation program*. Obtenido de https://scapy.net/
3. **IETF.** *RFC 1071: Computing the Internet Checksum*. Documentación del grupo de trabajo de ingeniería de Internet.
4. **IETF.** *RFC 2131: Dynamic Host Configuration Protocol*. 
5. **Reconocimiento Especial:** Fase de Troubleshooting analítico estructural, generación de scripts base y documentación técnica apoyada en Inteligencia Artificial.
