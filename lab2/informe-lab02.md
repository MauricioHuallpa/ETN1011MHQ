# Laboratorio 2 — FRRouting como router único en Linux

## 1. Datos del entorno

- **Estudiante:** Mauricio Huallpa Quino
- **Fecha:** 14 de octubre de 2026
- **Sistema:** Ubuntu 26.04.1 LTS (Resolute Raccoon), kernel 7.0.0-34-generic
- **Entorno:** máquina virtual VirtualBox sobre Windows (la guía está escrita
  para WSL2; se adaptó: la interfaz de salida es `enp0s3` en lugar de `eth0`
  y `systemd` ya estaba activo)
- **Software:** FRRouting 10.5.1 (paquete 10.5.1-1ubuntu4.1)

## 2. Objetivo

Instalar FRRouting, comprender su arquitectura sobre el kernel Linux y
administrar rutas conectadas, estáticas y de descarte desde `vtysh`, con un
único router y sin alterar la conectividad de la VM.

## 3. Topología

```text
VM Ubuntu (R1: Linux + FRR)
 ├── enp0s3   10.0.2.15/24   (NAT de VirtualBox, no modificada)
 └── dummy0   10.10.10.1/24  (interfaz de laboratorio)
```

## 4. Instalación y servicios

- `sudo apt install -y frr frr-pythontools iproute2 iputils-ping`
- `systemctl status frr`: active (running), "FRR Operational".
- `watchfrr` supervisa `mgmtd`, `zebra` y `staticd`. En esta versión de FRR,
  `zebra` y `staticd` siempre arrancan (el archivo `/etc/frr/daemons` lo
  indica) y solo se activan opcionalmente `bgpd`, `ospfd`, etc.

## 5. Configuración

```bash
sudo ip link add dummy0 type dummy
sudo ip addr add 10.10.10.1/24 dev dummy0
sudo ip link set dummy0 up
```

El kernel generó automáticamente la ruta conectada
`10.10.10.0/24 dev dummy0 proto kernel scope link src 10.10.10.1`.

En `vtysh`:

```text
configure terminal
ip route 198.51.100.0/24 blackhole
end
write memory
```

## 6. Verificación

- `show ip route` (FRR): rutas `K` (default por 10.0.2.2), `C` (conectadas)
  y `L` (local) para enp0s3; `C>* 10.10.10.0/24 dummy0`.
- Ruta estática en FRR: `Known via "static", distance 1, metric 0`,
  `Status: Installed`, `unreachable (blackhole)`.
- Misma ruta en el kernel: `blackhole 198.51.100.0/24 proto static metric 20`.
- Eliminada con `no ip route ...`: `% Network not in table`. Restaurada
  después.
- `ip route get 198.51.100.10` y `ping -c 2 198.51.100.10`: ambos devolvieron
  `Invalid argument`.
- `show interface dummy0`: up, line protocol up, tipo dummy.
- Persistencia: `write memory` escribió la ruta en `/etc/frr/frr.conf`
  (línea 7).

## 7. OSPF con un solo router

- Con `ospfd=no`: `show ip ospf neighbor` → `ospfd is not running`.
- Con `ospfd=yes` y `router ospf` / `network 10.10.10.0/24 area 0`:
  `dummy0` participa en el área 0 (costo 10, Hello 10 s, estado Waiting),
  pero `Neighbor Count is 0`.
- No hay vecinos porque no existe otro router OSPF conectado a esa red.
  OSPF funciona y emite Hellos, pero nadie responde.

## 8. Prueba de persistencia

Tras reiniciar la VM, `dummy0` ya no existía (se creó con `ip link`, que no
es persistente), mientras que la ruta blackhole y la configuración OSPF
seguían en `show running-config` (guardadas con `write memory`).

## 9. Preguntas de evaluación

1. **FRR vs kernel:** FRR calcula y administra rutas; Linux reenvía los
   paquetes y mantiene la tabla de rutas del kernel.
2. **`zebra`:** coordina interfaces y rutas entre los demonios de FRR y el
   kernel.
3. **`show ip route` vs `ip -4 route`:** la primera es la vista de FRR (con
   códigos, distancia administrativa y métrica de FRR); la segunda es la
   tabla instalada en el kernel (ej. `metric 20`).
4. **`write memory`:** guarda la configuración de FRR en `/etc/frr/frr.conf`;
   no guarda interfaces creadas con `ip link`.
5. **Sin vecinos OSPF:** no hay otro router OSPF en la misma red.
6. **`blackhole`:** descarta los paquetes destinados al prefijo; en Linux el
   envío local falla con `Invalid argument`.
7. **Equivalente Cisco de `write memory`:** `copy running-config
   startup-config`.
8. **IP fuera de `vtysh`:** FRR administra protocolos y rutas, mientras que
   Linux administra directamente las interfaces y sus direcciones.

## 10. Uso de asistencia de IA

Se utilizó Claude como apoyo para guiar la ejecución paso a paso y explicar
conceptos (diferencia entre la vista de FRR y del kernel, distintos errores de
ruta, comportamiento de `blackhole`). Todos los comandos fueron ejecutados y
verificados personalmente en la VM.

## 11. Conclusiones

FRR se apoya en el kernel: `zebra` sincroniza ambos mundos, por lo que una
misma ruta se ve distinta en FRR y en Linux. La configuración de FRR se
conserva con `write memory`, pero la de interfaces del kernel no. Un mismo
síntoma de red puede dar mensajes distintos según la causa: `Network is
unreachable` (sin ruta), `Invalid argument` (ruta blackhole) o timeout (el
paquete salió y no hubo respuesta). Finalmente, OSPF requiere vecinos: un solo
router puede ejecutar el protocolo, pero no formar adyacencias.
