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

El **CDP DoS Attack** explota la ausencia de autenticación en el protocolo **Cisco Discovery Protocol (CDP)** — un protocolo propietario de Capa 2 utilizado para el descubrimiento automático de dispositivos Cisco vecinos.

Mediante la inyección masiva de tramas CDP falsificadas con direcciones MAC aleatorias y estructuras TLV válidas, se provoca el **desbordamiento de la tabla de vecinos CDP** del switch objetivo, degradando los recursos del plano de control y afectando la estabilidad operacional del dispositivo.

---

## 🗺️ Topología de Red

### 📊 Direccionamiento IP

| Dispositivo | Interfaz | VLAN | Dirección IP | Rol |
|:-----------:|:--------:|:----:|:------------:|:---:|
| R1 | Eth0/0.10 | 10 | 10.7.25.1/24 | Gateway VLAN 10 |
| R1 | Eth0/0.20 | 20 | 10.7.20.1/24 | Gateway VLAN 20 |
| R1 | Eth0/0.99 | 99 | 10.7.99.1/24 | Gateway VLAN 99 |
| SW1 | Eth0/0 | Trunk | — | Enlace a R1 |
| SW1 | Eth0/1 | Trunk | — | Enlace a SW2 |
| SW1 | Eth0/3 | 99 | — | Puerto Atacante |
| Atacante | ens4 | 99 | 10.7.99.2/24 | Ubuntu + Scapy |
| VPC1 | eth0 | 10 | DHCP | Usuario final |
| VPC2 | eth0 | 20 | DHCP | Usuario final |

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
sudo python3 cdp_dos.py [cantidad_paquetes]

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
| 1️⃣ | Genera una MAC de origen aleatoria válida (unicast) |
| 2️⃣ | Construye TLVs CDP: Device ID, Address, Port, Capabilities, Platform |
| 3️⃣ | Calcula el checksum RFC 1071 para que IOS acepte el paquete |
| 4️⃣ | Encapsula en trama IEEE 802.3 LLC/SNAP hacia `01:00:0c:cc:cc:cc` |
| 5️⃣ | Envía masivamente saturando la tabla de vecinos CDP |

---

## 🛡️ Contramedidas

### Deshabilitar CDP por interfaz (recomendado)
```cisco
SW1(config)# interface ethernet 0/3
SW1(config-if)# no cdp enable
SW1(config-if)# end
SW1# write memory
```

### Deshabilitar CDP globalmente
```cisco
SW1(config)# no cdp run
SW1(config)# end
```

### Hardening adicional
- Implementar **Port Security** para limitar MACs por puerto
- Migrar a **LLDP** con control de temporizadores
- Apagar puertos sin uso con `shutdown`

---

## 📁 Archivos del Repositorio

| Archivo | Descripción |
|:-------:|-------------|
| [`cdp_dos.py`](cdp_dos.py) | Script principal del ataque |
| [`SaelGermanGarcia_2025-0725_Informe_P1.pdf`](SaelGermanGarcia_2025-0725_Informe_P1.pdf) | Documentación técnica completa |

---

## 🖼️ Capturas de Pantalla

- 📸 [Incremento progresivo de paquetes](Capturas%20de%20pantalla%20CDP%20DoS/Incremento%20progresivo%20de%20los%20c....png)
- 📸 [Tabla de Vecinos Saturada en SW1](Capturas%20de%20pantalla%20CDP%20DoS/Tabla%20de%20Vecinos%20Saturada%20.png)
- 📸 [Topología de Red](Capturas%20de%20pantalla%20CDP%20DoS/Topologia.png)
- 📸 [Contramedida Aplicada](Capturas%20de%20pantalla%20CDP%20DoS/contramedida.png)
---

## 📎 Recursos

📄 **Documentación Técnica:** [Ver Informe PDF](SaelGermanGarcia_2025-0725_Informe_P1.pdf)  
▶️ **Video Demostración:** [Ver en YouTube](https://youtube.com/playlist?list=PLV_dKVnYXf6dpmk3j8uXPHAZdbrkCQGAY)

---

## 📚 Referencias

1. Cisco Systems. *Cisco Discovery Protocol Configuration Guide*. Documentación oficial de Cisco IOS.
2. Scapy Project. *Scapy: Interactive packet manipulation program*. [https://scapy.net/](https://scapy.net/)
3. IETF. *RFC 1071: Computing the Internet Checksum*. Base matemática implementada en el código para la validación de tramas.
4. Reconocimiento especial: Troubleshooting y documentación apoyado en Inteligencia Artificial.

---

<div align="center">

⚠️ **AVISO LEGAL** ⚠️  
*Este script fue desarrollado exclusivamente con fines académicos y educativos.*  
*Su uso en redes sin autorización explícita es ilegal y éticamente inaceptable.*

</div>
