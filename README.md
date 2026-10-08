# Infraestructura 1 - FortiGate + VLAN + DMZ en GNS3

**Estudiante:** Reymond Daniel Guerrero Cruz  
**Matrícula:** 2024-0963  
**Asignatura:** Infraestructura 1  

---

## 🎥 Video de demostración

Puedes ver la demostración completa del laboratorio en el siguiente enlace:

[Ver video de demostración en YouTube](https://youtu.be/GwzhK92OBJs)

---

## Descripción del proyecto

Este laboratorio implementa una infraestructura de red segmentada y protegida mediante FortiGate, MikroTik, VLAN y una DMZ utilizando GNS3.

El objetivo principal es separar la red de usuarios de la red de servidores, controlar el acceso entre segmentos y aplicar políticas de seguridad específicas para servicios HTTPS, SSH y acceso a Internet.
---

## Topología

La infraestructura está compuesta por:

- 1 FortiGate.
- 1 MikroTik CHR.
- 2 switches.
- VLAN 10 para usuarios.
- VLAN 20 para administración.
- VLAN 30 para servidores DMZ.
- VLAN 99 utilizada como Parking VLAN.
- 2 servidores web.
- 1 servidor de base de datos.
- Clientes de prueba para VLAN 10 y VLAN 20.
- Conexión simulada hacia Internet.

---

## Direccionamiento IP

| Segmento | Red | Gateway |
|---|---|---|
| VLAN 10 - Usuarios | `10.24.96.0/25` | `10.24.96.1` |
| VLAN 20 - Administración | `10.24.97.0/25` | `10.24.97.1` |
| DMZ - VLAN 30 | `10.24.96.128/28` | `10.24.96.129` |
| Tránsito MikroTik-FortiGate | `10.10.10.0/30` | FortiGate `10.10.10.1` |
| WAN | `198.51.100.96/30` | ISP `198.51.100.97` |

---

## Servidores de la DMZ

| Servidor | IP | Servicios |
|---|---|---|
| WEB-CAJA | `10.24.96.130` | HTTPS/443, SSH/22 |
| WEB-INVENTARIO | `10.24.96.131` | HTTPS/443, SSH/22 |
| DB-SERVER | `10.24.96.132` | MariaDB/3306, SSH/22 |

---

## Políticas de seguridad del FortiGate

### VLAN 20 hacia servidores por SSH

La VLAN 20 es la única red autorizada para acceder mediante SSH a los servidores de la DMZ.

Política:

`VLAN20_TO_DMZ_SSH`

- Origen: `VLAN20-NET`
- Destino: `DMZ-SERVERS`
- Servicio: `SSH`
- Acción: `ACCEPT`

### VLAN 10 hacia Sistema de Caja

Los usuarios de VLAN 10 pueden acceder mediante HTTPS únicamente al Sistema de Caja.

Política:

`VLAN10_TO_CAJA_HTTPS`

- Origen: `VLAN10-NET`
- Destino: `WEB-CAJA`
- Servicio: `HTTPS`
- Acción: `ACCEPT`

### VLAN 10 hacia Sistema de Inventario

El acceso desde VLAN 10 hacia el Sistema de Inventario está bloqueado.

El tráfico que no coincide con las políticas autorizadas es bloqueado mediante la política **Implicit Deny** del FortiGate.

### DMZ hacia Internet

Los servidores de la DMZ no poseen acceso abierto a Internet.

Política:

`DMZ_TO_UPDATES_ONLY`
