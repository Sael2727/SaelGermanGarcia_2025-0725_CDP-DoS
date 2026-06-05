# 🛡️ CDP DoS Attack — Seguridad de Redes

<div align="center">

![Python](https://img.shields.io/badge/Python-3.x-blue?style=for-the-badge&logo=python)
![Scapy](https://img.shields.io/badge/Scapy-Latest-green?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Linux-orange?style=for-the-badge&logo=linux)
![License](https://img.shields.io/badge/Uso-Educativo-red?style=for-the-badge)

**Sael Germán García** | Matrícula: `2025-0725`  
Asignatura: Seguridad de Redes | Profesor: Jonathan Rondón  
Instituto Tecnológico de las Américas — ITLA | 2026

</div>

---

## 📋 Descripción del Ataque

El **CDP DoS Attack** explota la ausencia de autenticación en el protocolo **Cisco Discovery Protocol (CDP)** — un protocolo propietario de Capa 2 utilizado para el descubrimiento automático de dispositivos Cisco vecinos. Mediante la inyección masiva de tramas CDP falsificadas con direcciones MAC aleatorias y estructuras TLV válidas, se provoca el **desbordamiento de la tabla de vecinos CDP** del switch objetivo, degradando los recursos del plano de control y afectando la estabilidad operacional del dispositivo.

---

## 🗺️ Topología de Red

### 📊 Segmentación de VLANs

| VLAN ID | Nombre | Segmento IP | Descripción |
|:-------:|:------:|:-----------:|-------------|
| 10 | Usuarios | 10.7.25.0/24 | Usuarios finales legítimos (VPC1) |
| 20 | Servidores | 10.7.20.0/24 | Zona de servidores locales (VPC2) |
| 99 | Atacante | 10.7.99.0/24 | VLAN nativa — Máquina atacante Ubuntu |

### 📊 Matriz de Direccionamiento IP

| Dispositivo | Interfaz | VLAN | Dirección IP | Máscara | Detalle |
|:-----------:|:--------:|:----:|:------------:|:-------:|---------|
| R1 | Eth0/0.10 | 10 | 10.7.25.1 | /24 | Gateway VLAN 10 |
| R1 | Eth0/0.20 | 20 | 10.7.20.1 | /24 | Gateway VLAN 20 |
| R1 | Eth0/0.99 | 99 | 10.7.99.1 | /24 | Gateway VLAN 99 |
| SW1 | Eth0/0 | Trunk | — | — | Enlace troncal hacia R1 |
| SW1 | Eth0/1 | Trunk | — | — | Enlace troncal hacia SW2 |
| SW1 | Eth0/3 | 99 | — | — | Puerto acceso Nodo Atacante |
| SW2 | Eth0/1 | Trunk | — | — | Enlace troncal hacia SW1 |
| SW2 | Eth0/0 | 10 | DHCP | /24 | VPC1 |
| SW2 | Eth0/2 | 20 | DHCP | /24 | VPC2 |
| Atacante | ens4 | 99 | 10.7.99.2 | /24 | Ubuntu + Scapy |

---

## ⚙️ Requisitos

```bash
# Sistema Operativo
Ubuntu Linux (recomendado)

# Dependencias
sudo apt update && sudo apt install -y python3-scapy python3-pip

# Privilegios requeridos
sudo / root
```

---

## 🚀 Uso

```bash
# Sintaxis
sudo python3 cdp_dos.py [paquetes_a_enviar]

# Ejemplo — enviar 1000 paquetes
sudo python3 cdp_dos.py 1000

# Verificar impacto en SW1
show cdp neighbors
show cdp neighbors detail
```

---

## 🔬 ¿Cómo funciona?

| Paso | Descripción |
|:----:|-------------|
| 1️⃣ | `random_mac()` — Genera MACs unicast aleatorias válidas aplicando máscara `& 0xFC` al primer octeto |
| 2️⃣ | `cdp_checksum()` — Calcula el checksum RFC 1071 para que IOS acepte el paquete como válido |
| 3️⃣ | `build_cdp_packet()` — Construye TLVs CDP: Device ID, Version, Platform, Address, Port, Capabilities, VLAN |
| 4️⃣ | Encapsula en trama IEEE 802.3 LLC/SNAP hacia `01:00:0c:cc:cc:cc` |
| 5️⃣ | `cdp_flood()` — Envía masivamente saturando la tabla de vecinos CDP del switch |

---

## 🛡️ Contramedidas

### Desactivación por interfaz específica (recomendado)
```cisco
SW1(config)# interface ethernet 0/3
SW1(config-if)# no cdp enable
SW1(config-if)# end
SW1# write memory
```

### Desactivación global de CDP
```cisco
SW1(config)# no cdp run
SW1(config)# end
```

### Recomendaciones de Hardening adicionales
- Migrar a **LLDP (IEEE 802.1AB)** con control de temporizadores
- Implementar **Port Security** para limitar MACs por puerto
- Asignar VLANs nativas distintas de VLAN 1 y apagar puertos sin uso

---

## 📁 Archivos del Repositorio

| Archivo | Descripción |
|:-------:|-------------|
| [`cdp_dos.py`](cdp_dos.py) | Script principal del ataque |
| [`SaelGermanGarcia_2025-0725_CDP_DoS_P1.pdf`](SaelGermanGarcia_2025-0725_CDP_DoS_P1.pdf) | Documentación técnica completa |

---

## 🖼️ Capturas de Pantalla

- 📸 [Incremento progresivo de paquetes](Capturas%20de%20pantalla%20CDP%20DoS/Incremento%20progresivo%20de%20los%20contradores%20de%20paquetes.png)
- 📸 [Tabla de Vecinos Saturada en SW1](Capturas%20de%20pantalla%20CDP%20DoS/Tabla%20de%20Vecinos%20Saturada%20.png)
- 📸 [Topología de Red](Capturas%20de%20pantalla%20CDP%20DoS/Topologia.png)
- 📸 [Contramedida Aplicada](Capturas%20de%20pantalla%20CDP%20DoS/contramedida.png)

---

## 📎 Recursos

📄 **Documentación Técnica:** [Ver Informe PDF](SaelGermanGarcia_2025-0725_CDP_DoS_Informe_P1.pdf)  
▶️ **Video Demostración:** [Ver en YouTube](https://youtube.com/playlist?list=PLV_dKVnYXf6dpmk3j8uXPHAZdbrkCQGAY)

---

## 📚 Referencias

1. Cisco Systems. *Cisco Discovery Protocol Configuration Guide*. Documentación oficial de Cisco IOS.
2. Scapy Project. *Scapy: Interactive packet manipulation program*. [https://scapy.net/](https://scapy.net/)
3. IETF. *RFC 1071: Computing the Internet Checksum*. Base matemática implementada en el código.
4. Reconocimiento especial: Troubleshooting, base del script y documentación apoyado en Inteligencia Artificial.

---

<div align="center">

⚠️ **AVISO LEGAL** ⚠️  
*Este script fue desarrollado exclusivamente con fines académicos y educativos.*  
*Su uso en redes sin autorización explícita es ilegal y éticamente inaceptable.*

</div>
