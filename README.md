# DNS Spoofing Lab
Video https://youtu.be/YQeJh6kMIQs?si=EKiGmorpoBQq5Zej 
## Ejecución del Script

```bash
sudo python3 dnsspoofing.py -i (adaptador_de_red) -d itla.edu.do -r (ip_maquina_atacante)
````

---

## Objetivo del Script

El objetivo de este script es permitir la realización de un ataque de **DNS Spoofing**, el cual consiste en suplantar la respuesta del servidor DNS.

Este ataque puede provocar que:

* El atacante se haga pasar por el servidor DNS.
* Se pueda interceptar el tráfico de la víctima.
* Se modifique la información que la víctima visualiza al intentar acceder a un dominio específico.
* Se redirija a la víctima hacia una dirección IP controlada por el atacante.

---

## Topología Utilizada

### Dispositivos:

* 3 Switches
* 1 Router
* 4 Hosts Finales

### VLANs Configuradas:

* **VLAN 10** → 192.168.10.0/24
* **VLAN 20** → 192.168.20.0/24
* **VLAN 30** → 192.168.30.0/24
* **VLAN 99** → VLAN Nativa

### Configuración de Red:

* Inter-VLAN Routing mediante **Router-on-a-Stick**
* Asignación de direcciones IP mediante **DHCP configurado en el router**

---

## ⚙️ Parámetros Requeridos

* El atacante y la víctima deben estar en la **misma red local (LAN)**.
* Ambos deben tener conectividad entre sí.
* Es necesario realizar previamente una técnica de **Man-in-the-Middle (MiTM)**.
* En esta práctica se utilizó **ARP Spoofing** para posicionarse en medio de la comunicación.

> El script de ARP Spoofing utilizado es el siguiente:
> [https://github.com/RichardMC7/MitM-ARP-SCAPY.git](https://github.com/RichardMC7/MitM-ARP-SCAPY.git)

---

## Requisitos para Utilizar la Herramienta

### 1️Instalar Python 3

```bash
sudo apt install python3 -y
```

---

### Instalar Scapy

```bash
sudo apt install python3-pip -y
pip3 install scapy
```

---

## Medidas de Mitigación

### 1. Bloquear uso de DNS externos

```bash
access-list 100 deny udp any any eq 53
access-list 100 permit ip any any

interface g0/0
 ip access-group 100 in
```

---

### 2. Permitir únicamente DNS interno

Ejemplo:

```bash
access-list 101 permit udp any host 192.168.10.10 eq 53
access-list 101 deny udp any any eq 53
access-list 101 permit ip any any

interface g0/0
 ip access-group 101 in
```

---

### 3. Implementar uRPF (Unicast Reverse Path Forwarding)

```bash
interface g0/0
 ip verify unicast source reachable-via rx
```

Esto ayuda a prevenir paquetes con direcciones IP falsificadas.

---

### 4. Configurar Dynamic ARP Inspection (DAI)

```bash
ip arp inspection vlan 10
```

Esto permite que el router/switch valide las respuestas ARP usando la tabla generada por DHCP Snooping.

---

## Nota Importante

El script está configurado específicamente para el dominio:

```
itla.edu.do
```

Este laboratorio debe utilizarse únicamente en un entorno controlado con fines educativos.

```
