# Laboratorio 1 — Redes virtuales con Linux

## 1. Datos del entorno
- **Estudiante:** Mauricio Huallpa Quino
- **Fecha:** 24 de septiembre de 2026
- **Distribución utilizada:** Ubuntu 26.04.1 LTS (Resolute Raccoon)
- **Entorno:** Máquina virtual (VirtualBox) sobre host Windows
- **Usuario del sistema:** vboxuser
- **Versión del kernel:** Linux 7.0.0-34-generic (x86_64), compilado el 2 de septiembre de 2026

## 2. Objetivo

Construir y verificar una topología de red virtual mínima utilizando *network
namespaces* de Linux, simulando dos equipos independientes (hostA y hostB)
conectados mediante un enlace punto a punto (par veth), aplicando
direccionamiento IPv4, verificación de conectividad y análisis con
herramientas estándar de Linux (`ip`, `ping`, `tcpdump`).		 

## 3. Topología

Se crearon dos *network namespaces*, `hostA` y `hostB`, cada uno simulando un
host independiente con su propia pila de red (interfaces, rutas, tabla de
vecinos). Ambos se conectaron mediante un par de interfaces virtuales `veth`
(`vethA` y `vethB`), que actúan como un cable Ethernet directo entre los dos
namespaces.

           hostA                         hostB
vethA: 10.10.1.1/30 <——veth——> vethB: 10.10.1.2/30 
 
## 4. Direccionamiento

Se utilizó el bloque `10.10.1.0/30`, que al dejar solo 2 bits para host
produce 4 direcciones totales:

- **Dirección de red:** 10.10.1.0
- **Direcciones utilizables:** 10.10.1.1 (hostA) y 10.10.1.2 (hostB)
- **Dirección de broadcast:** 10.10.1.3
- **Cantidad de direcciones utilizables:** 2

No se requiere gateway en esta topología porque ambos extremos (10.10.1.1 y
10.10.1.2) pertenecen a la misma subred /30 — es un enlace punto a punto
directo, sin necesidad de salto intermedio.

## 5. Construcción

Secuencia de comandos utilizada para construir la topología:

```bash
# Crear los namespaces
sudo ip netns add hostA
sudo ip netns add hostB

# Crear el par veth
sudo ip link add vethA type veth peer name vethB

# Mover cada extremo a su namespace
sudo ip link set vethA netns hostA
sudo ip link set vethB netns hostB

# Asignar direcciones IP
sudo ip netns exec hostA ip addr add 10.10.1.1/30 dev vethA
sudo ip netns exec hostB ip addr add 10.10.1.2/30 dev vethB

# Activar las interfaces
sudo ip netns exec hostA ip link set lo up
sudo ip netns exec hostA ip link set vethA up
sudo ip netns exec hostB ip link set lo up
sudo ip netns exec hostB ip link set vethB up
```

## 6. Verificación
### Interfaces

hostA:
4: vethA@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 state UP
link/ether 5e:10:cd:11:61:ea link-netns hostB

hostB:
3: vethB@if4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 state UP
link/ether 2e:fd:76:fc:85:7d link-netns hostA

### Direcciones

hostA: inet 10.10.1.1/30 scope global vethA
hostB: inet 10.10.1.2/30 scope global vethB

### Rutas

hostA: 10.10.1.0/30 dev vethA proto kernel scope link src 10.10.1.1
hostB: 10.10.1.0/30 dev vethB proto kernel scope link src 10.10.1.2

Esta ruta fue generada automáticamente por el kernel (`proto kernel`) al
asignar la dirección IP con su prefijo — no se agregó manualmente. No se
necesitó ruta por defecto ni gateway, dado que el destino está en la misma
red directamente conectada.

### Vecinos 
### Conectividad

hostA → hostB (ping -c 4 10.10.1.2): 0% pérdida, RTT 0.065–0.173 ms
hostB → hostA (ping -c 4 10.10.1.1): 0% pérdida, RTT 0.062–0.208 ms

Conectividad bidireccional confirmada entre ambos namespaces. Las latencias
submilimétricas se explican porque el tráfico nunca sale al hardware físico:
viaja completamente en memoria dentro del mismo kernel.

### Reconocimiento de red del sistema (interfaz física, previo a los namespaces)

Interfaces: lo, enp0s3
IP de enp0s3: 10.0.2.15/24 (red NAT interna de VirtualBox)
Ruta por defecto: via 10.0.2.2 dev enp0s3
Gateway: 10.0.2.2
Vecino conocido: 10.0.2.2 (52:54:00:12:35:00)

ping -c 4 8.8.8.8 → 0% pérdida, RTT ~105-136 ms
ping -c 4 google.com → 0% pérdida, resolvió a 64.233.186.138
traceroute 8.8.8.8 → solo visible el primer salto (10.0.2.2, gateway NAT
de VirtualBox); saltos siguientes sin respuesta, comportamiento típico
detrás de NAT/firewall que descarta paquetes ICMP Time Exceeded.

## 7. Captura y análisis de tráfico
## 8. Falla y diagnóstico
## 9. Uso de OpenCode
## 10. Conclusiones

