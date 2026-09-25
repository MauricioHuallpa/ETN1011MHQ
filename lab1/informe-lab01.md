 # Laboratorio 1 — Redes virtuales con Linux
 ## 1. Datos del entorno
- **Estudiante:** Mauricio Huallpa
- **Fecha:** 24 de septiembre de 2026
- **Distribución utilizada:** Ubuntu 26.04.1 LTS (Resolute Raccoon)
- **Entorno:** Máquina virtual (VirtualBox) sobre host Windows
- **Usuario del sistema:** vboxuser
- **Versión del kernel:** Linux 7.0.0-34-generic (x86_64), compilado el 2 de septiembre de 2026
 ## 2. Objetivo
 ## 3. Topología
 dos namespaces (hostA, hostB) conectados por un par veth (vethA-vethB), direcciones 10.10.1.1/30 y 10.10.1.2/30
 ## 4. Direccionamiento
 ## 5. Construcción
 ## 6. Verificación
 ### Interfaces
 ### Direcciones
 ### Rutas
 ### Vecinos
 ### Conectividad
- ping -c 4 8.8.8.8 → 0% pérdida, RTT ~105-136 ms
- ping -c 4 google.com → 0% pérdida, resolvió a 64.233.186.138
- traceroute 8.8.8.8 → solo visible el primer salto (10.0.2.2, gateway NAT
  de VirtualBox); saltos siguientes sin respuesta, comportamiento típico
  detrás de NAT/firewall que descarta paquetes ICMP Time Exceeded.
 ## 7. Captura y análisis de tráfico
 ## 8. Falla y diagnóstico
 ## 9. Uso de OpenCode
 ## 10. Conclusiones 

