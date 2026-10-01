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

Comando: sudo ip netns exec hostA tcpdump -i vethA -n icmp

Se capturaron 8 paquetes (4 echo request + 4 echo reply), correspondientes
al ping de hostB (10.10.1.2) hacia hostA (10.10.1.1):

00:59:58.185856 IP 10.10.1.2 > 10.10.1.1: ICMP echo request, id 7433, seq 1
00:59:58.185886 IP 10.10.1.1 > 10.10.1.2: ICMP echo reply,   id 7433, seq 1
... (seq 2, 3, 4 análogos)

Análisis:
- El "id" (7433) es el mismo en las 8 líneas: identifica que todos
  pertenecen al mismo proceso de ping.
- El "seq" (1-4) permite emparejar cada solicitud con su respuesta
  y detectar pérdidas si algún número no tuviera su par.
- El tiempo entre la solicitud y su respuesta (ej. 00:59:58.185856 →
  00:59:58.185886, apenas 30 microsegundos) confirma que el tráfico
  nunca sale al hardware físico: viaja completamente en memoria dentro
  del kernel, por eso las latencias del ping fueron de microsegundos.
- "0 packets dropped by kernel" confirma que no hubo pérdida ni
  saturación del buffer de captura.

## 8. Falla y diagnóstico

**Falla provocada:** eliminación de la dirección IP de hostB
(sudo ip netns exec hostB ip addr del 10.10.1.2/30 dev vethB)

**Diagnóstico paso a paso:**

| Verificación        | Resultado                                              |
|---------------------|--------------------------------------------------------|
| Interfaz (ip link)  | vethB sigue existiendo y en estado UP                  |
| Dirección (ip addr) | IPv4 10.10.1.2 ausente; solo quedó IPv6 link-local     |
| Ruta (ip route)     | Tabla de rutas vacía                                   |
| Ping                | "Network is unreachable" (error inmediato, no timeout) |

**Análisis:** la interfaz física/virtual no se vio afectada, lo que
descarta un problema de capa de enlace. La ruta automática que el kernel
había generado en la Sección 10 desapareció junto con la dirección IP,
porque esa ruta dependía directamente de que la IP existiera. El error
"Network is unreachable" es generado localmente por el kernel de hostB
antes de intentar enviar el paquete, a diferencia de un timeout (que
indicaría que el paquete sí salió pero no hubo respuesta). Esto demuestra
que el problema estaba en la configuración local de hostB, no en el
enlace ni en hostA.

**Solución:** se restauró la dirección IP con
`ip addr add 10.10.1.2/30 dev vethB`, lo cual regeneró automáticamente la
ruta y restableció la conectividad (0% de pérdida, confirmado con ping).

## 9. Uso de OpenCode

Se utilizó asistencia de IA (Claude) como apoyo conceptual durante el
desarrollo del laboratorio: explicación de conceptos (network namespaces,
veth, direccionamiento /30, diferencia entre "Network unreachable" y
timeout), guía paso a paso en la ejecución de comandos, y ayuda para
resolver problemas de entorno no relacionados con el contenido del lab
(configuración de VirtualBox, portapapeles compartido, autenticación SSH
con GitHub). Todos los comandos fueron ejecutados y verificados
personalmente en la VM; las interpretaciones de los resultados (Actividades
1-3, diagnóstico de falla) fueron razonadas y comprendidas antes de
documentarlas, de forma que puedan explicarse sin apoyo externo en la
defensa oral.

## 10. Conclusiones

El laboratorio permitió comprender de forma práctica cómo Linux puede
simular múltiples equipos de red independientes dentro de una sola máquina,
utilizando *network namespaces* como mecanismo de aislamiento (cada uno con
su propia tabla de interfaces, direcciones, rutas y vecinos) y pares `veth`
como enlaces virtuales punto a punto entre ellos.

Se verificó que una dirección IP y su ruta asociada no son conceptos
independientes: el kernel genera automáticamente la ruta "scope link" en
cuanto se asigna una IP con su prefijo, y esa misma ruta desaparece si la
IP se elimina — lo cual se comprobó directamente al provocar y diagnosticar
una falla real.

El uso de `ip` (link, addr, route, neigh) resultó suficiente para
diagnosticar el estado completo de una interfaz de red, siguiendo un orden
lógico: primero la existencia física de la interfaz, luego su
direccionamiento, luego el enrutamiento, y finalmente la conectividad
efectiva. Esta secuencia permite distinguir con precisión en qué capa se
origina un problema de red, algo evidenciado por la diferencia entre un
error de tipo "Network is unreachable" (fallo local, de ruta) y un timeout
de ping (fallo en el otro extremo o en el medio).

La captura de tráfico con `tcpdump` permitió observar en tiempo real los
paquetes ICMP (echo request/reply) intercambiados entre los namespaces,
confirmando a nivel de paquete lo que las pruebas de `ping` ya habían
mostrado a nivel de resultado.

Finalmente, el laboratorio también sirvió para familiarizarme con el flujo
de trabajo típico de un entorno Linux real: configuración de una máquina
virtual desde cero, autenticación SSH con GitHub, y control de versiones
mediante Git para documentar el avance del trabajo con commits incrementales.
