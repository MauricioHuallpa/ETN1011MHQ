# Laboratorio — Enlaces Internacionales de Internet en Bolivia

**Estudiante:** Mauricio Huallpa Quino
**Materia:** ETN1011 — Sistemas de Comunicación 2
**Fecha de las consultas:** 10 de noviembre de 2026 (hora de Bolivia)
**Entorno de las mediciones:** VM Ubuntu 26.04 en VirtualBox (red NAT), desde La Paz. Los `dig` y `ping` salen de mi conexión; los resultados pueden cambiar según el resolvedor y la red.

---

## 1. Infraestructura de fibra

| Infraestructura identificada | Ubicación / extremos publicados | Fuente (URL) |
|---|---|---|
| Fibra terrestre 1 | Operador: Empresa Nacional de Telecomunicaciones S.A. Tramo La Paz – Patacamaya, distancia 90.31 según la ficha. Estado: Operational | https://bbmaps.itu.int/bbmaps/ (UIT Broadband Maps) |
| Fibra terrestre 2 | Operador: Millicom Bolivia. Tramo La Paz – Calamarca, distancia 56.84 según la ficha. Estado: Operational | https://bbmaps.itu.int/bbmaps/ (UIT Broadband Maps) |
| Cable submarino 1 | South America-1 (SAm-1). Costa: Pacífico, estación de amarre: Arica, Chile. Listo para servicio: marzo de 2001, longitud 25,000 km, propietario: Telxius, proveedor: SubCom | https://www.submarinecablemap.com/ (ficha «South America-1 (SAm-1)») |
| Cable submarino 2 | South Pacific Cable System (SPCS) / Mistral. Costa: Pacífico, estación de amarre: Arica, Chile. Listo para servicio: agosto de 2021, longitud 7,300 km, propietarios: América Móvil (Claro) y Telxius, proveedor: SubCom | https://www.submarinecablemap.com/ (ficha «South Pacific Cable System (SPCS)/Mistral») y https://www.submarinecablemap.com/landing-point/arica-chile |

**Contraste con la ATT:** en https://smi.att.gob.bo/ pude abrir el documento «Estado de situación de las telecomunicaciones en Bolivia, primer semestre gestión 2025» y un mapa con fibra óptica, radiobases y cobertura móvil. En mi navegador el mapa se ve pero no responde a clics, así que no pude identificar los tramos La Paz – Patacamaya ni La Paz – Calamarca en él. El contraste de un tramo con la UIT es **no verificable**.

**Diferencia (qué ofrece el mapa y qué no demuestra):** los mapas muestran operadores, extremos, estado y, en los cables, propietarios y estaciones de amarre. No demuestran qué ISP boliviano usa realmente un tramo o un cable, con qué capacidad ni bajo qué contrato, ni por dónde viaja el tráfico de un usuario. Bolivia no tiene costa, por lo que ninguno de los dos cables llega al país: ambos amarran en Arica, Chile. Que esa sea la salida más cercana a La Paz es una **inferencia** mía.

---

## 2. ASN y proveedores

| Red / servicio | ASN encontrado | Titular o nombre visible | Upstream probable (solo ISP) |
|---|---|---|---|
| ENTEL | AS6568 | Entel Bolivia (BGP.Tools). Titular RDAP: Empresa Nacional de Telecomunicaciones Sociedad Anónima | AS3356 Lumen (también aparece AS12956 Telxius) |
| AXS | AS26210 | AXS Bolivia S.A. | AS12956 Telxius (también AS174 Cogent) |
| COMTECO | AS27839 | Comteco Ltda | AS6568 Entel (también AS12956 Telxius) |
| COTAS | AS25620 | COTAS LTDA. | AS12956 Telxius (también AS271253 Link Brasil y AS1031 Peer 1031) |
| TIGO | AS27882 | Telefónica Celular de Bolivia S.A. (BGP.Tools y RDAP) | AS12956 Telxius (también AS18678 Internexa) |
| Google / YouTube | AS15169 | Google LLC | — |
| Netflix | AS2906 | Netflix Streaming Services Inc. | — |
| Meta / Facebook | AS32934 | Facebook, Inc. | — |
| TikTok / ByteDance | AS396986 | Bytedance, Inc. (TikTok) | — |

**Verificación en LACNIC RDAP (dos ASN):**
- AS6568: titular registrado «EMPRESA NACIONAL DE TELECOMUNICACIONES SOCIEDAD ANONIMA» (BO-ESEN-LACNIC), asignación directa registrada en 1996. Coincide con «Entel Bolivia» de BGP.Tools; la diferencia es razón social frente a nombre comercial. Contactos en La Paz.
- AS27882: titular «Telefónica Celular de Bolivia S.A.» (BO-TCBS1-LACNIC), registrado en 2007. Coincide con BGP.Tools. Contactos en Santa Cruz.
- Fuentes: https://rdap.lacnic.net/rdap/autnum/AS6568 y https://rdap.lacnic.net/rdap/autnum/AS27882

**Notas:**
- Los upstreams son los que muestra BGP.Tools; los anoto como **probables**, no como contrato demostrado.
- TikTok: encontré la red propia de ByteDance (AS396986, tipo Content). No asumo que sea el único ASN del servicio; los proveedores o CDN externos que usa en Bolivia son **no verificables**.
- Netflix: anoto AS2906 como el ASN encontrado, sin afirmar que sea el único.

Fuentes (BGP.Tools): https://bgp.tools/as/6568 · https://bgp.tools/as/26210 · https://bgp.tools/as/27839 · https://bgp.tools/as/25620 · https://bgp.tools/as/27882 · https://bgp.tools/as/15169 · https://bgp.tools/as/2906 · https://bgp.tools/as/32934 · https://bgp.tools/as/396986

---

## 3. Peering, tránsito y BGP

| Relación entre ASN | Tipo: tránsito / peering / incierto | Evidencia (herramienta/URL) |
|---|---|---|
| AS6568 (Entel) – AS3356 (Lumen) | Tránsito probable | BGP.Tools, pestaña Connectivity de AS6568: Lumen aparece como upstream (y también como peer). https://bgp.tools/as/6568 |
| AS6568 (Entel) – AS15169 (Google) | Peering probable | BGP.Tools, pestaña Connectivity de AS6568: Google aparece solo como peer, no como upstream ni downstream. https://bgp.tools/as/6568 |
| AS27839 (Comteco) – AS6568 (Entel) | Tránsito probable | BGP.Tools, ficha de Comteco: Entel aparece como upstream. https://bgp.tools/as/27839 |

**PeeringDB (datos autodeclarados por cada red):**

| | Entel (AS6568) | Google (AS15169) |
|---|---|---|
| Política de peering | Selectiva | Selectiva |
| Varias ubicaciones | No necesario | Preferido |
| Requisito de ratio | Sí | No |
| Requisito de contrato | Solo privado | No necesario |
| IXP declarados | PIT Bolivia, PIT Perú (Lima), Equinix São Paulo | Muchos; en la lista que copié aparecen, entre otros, Equinix São Paulo, IX.br (PTT.br) São Paulo y Río de Janeiro y AR-IX Cabase (Argentina). PIT Bolivia no aparece en esa lista |

- Tigo declara presencia en PIT Bolivia (BGP.Tools, pestaña IX).
- Entel y Google declaran presencia en **Equinix São Paulo**. Esto no prueba una sesión activa de peering entre ellos.
- Fuentes: https://www.peeringdb.com/org/16536 (Entel) y https://www.peeringdb.com/org/574 (Google).

**RIPEstat:** el prefijo `190.129.48.0/20` (el que contiene la IP de `www.entel.bo`) lo anuncia **AS6568**. Fue visible para el 99.4 % de 320 peers IPv4 completos de RIS el 2026-11-10 a las 16:00; se vio por primera vez en 2011 y LACNIC lo asignó en 2006 (BO). **AS_PATH: no observable** en lo que pude capturar. Fuente: https://stat.ripe.net/ (búsqueda del prefijo).

**¿Por qué aparecer como vecino BGP no prueba un contrato comercial ni una ruta física?** Porque ser vecino BGP solo significa que dos redes intercambian rutas, sin decir si hay un contrato de tránsito, un acuerdo de peering o ninguno; y un AS_PATH es una lista de redes, no de cables ni de ciudades.

---

## 4. DNS y latencia

### Registros DNS

| Dominio | A (IP o sin respuesta) | NS (uno) | MX (uno o ausente) | TTL (s) |
|---|---|---|---|---|
| impuestos.bo | Sin respuesta (NXDOMAIN, en el resolvedor local y en 1.1.1.1) | — | — | 900 (TTL negativo del SOA de `bo.`) |
| umsa.bo | 200.7.161.56 | jupiter.umsa.bo | aspmx.l.google.com (pref. 1) | 3600 |
| ISP elegido: entel.bo | Sin respuesta en el dominio raíz (NOERROR sin registro A). `www.entel.bo` → 190.129.60.166 | ns.entelnet.bo | entel-bo.mail.protection.outlook.com | 25 (NS), 60 (MX), 31 (A de www) |
| Empresa privada: lacascada.com.bo (La Cascada S.A.) | 69.167.179.79. `www` → 104.21.28.182 y 172.67.147.20 | adele.ns.cloudflare.com | lacascada-com-bo.mail.protection.outlook.com (pref. 0) | 300 (A y MX), 86400 (NS) |
| Consulta adicional: impuestos.gob.bo | 170.82.244.41 | kami.ns.cloudflare.com | antispam22.weblinksrl.com | 300 (A), 86400 (NS y MX) |
| Consulta adicional: tigo.com.bo | 52.0.229.131 | dns1.wimaxtigo.bo | tigo-com-bo.mail.protection.outlook.com (hay otros MX, entre ellos uno de Trend Micro y otro de Amazon) | 3600 (A) |

**AAAA, SOA y TXT para dos dominios:**

| Dominio | AAAA | SOA | TXT |
|---|---|---|---|
| umsa.bo | 2801:104:dc:a161::a56 (TTL 60) | Primario marte.umsa.bo, serial 2022032360 | Verificaciones de Google y GlobalSign, y un SPF que autoriza a Google, Mandrill y una IP propia, terminando en `-all` |
| impuestos.gob.bo | Ausente (sin IPv6) | Primario kami.ns.cloudflare.com | Varias verificaciones (Google, Microsoft, Cisco) y un SPF con `ip4:170.82.244.0/23` |

**Comparación con Google Admin Toolbox Dig:** consulté `umsa.bo` tipo MX. Devolvió los mismos 5 servidores de Google con las mismas preferencias (1, 5, 5, 10, 10) y TTL «1 hour» (= 3600 s) que mi consulta local; solo cambió el orden. Fuente: https://toolbox.googleapps.com/apps/dig/. Google Public DNS y DNSViz no los consulté (eran opcionales).

**¿DNS permite saber por sí solo en qué país está alojado físicamente el servicio?** No. El DNS solo traduce nombres a direcciones y dice quién administra la zona. Por ejemplo, `impuestos.gob.bo` y `lacascada.com.bo` usan servidores de nombres de Cloudflare, y `tigo.com.bo` apunta a una IP dentro de un prefijo anunciado por AS16509 (Amazon.com, Inc., según BGP.Tools), pero ninguna de esas respuestas dice dónde está el servidor.

### Latencia con `ping` (5 solicitudes ICMP)

| Dominio | IP resuelta | RTT promedio (ms) | Pérdida (%) | Observación |
|---|---|---:|---:|---|
| impuestos.bo | No resuelve | — | — | «Name or service not known», coherente con el NXDOMAIN |
| umsa.bo | 200.7.161.56 | 60.151 | 0 | mín 25.7, máx 140.9, mdev 41.7 |
| ISP elegido: www.entel.bo | 190.129.60.166 | 40.777 | 0 | mín 16.3, máx 109.3, mdev 35.1. `entel.bo` sin `www` no tiene registro A: «No address associated with hostname» |
| lacascada.com.bo | 69.167.179.79 | 162.379 | 0 | mín 141.0, máx 212.2 |
| www.lacascada.com.bo | 172.67.147.20 | 129.618 | 0 | mín 98.6, máx 189.0 |
| impuestos.gob.bo | 170.82.244.41 | — | 100 | ICMP sin respuesta |
| tigo.com.bo | 52.0.229.131 | — | 100 | ICMP sin respuesta |

El RTT incluye ida y vuelta y procesamiento de red; no es distancia física, ni latencia DNS, ni rendimiento web. Una respuesta puede venir de un proxy o CDN y no del servidor de origen. Los destinos sin respuesta pueden estar filtrando ICMP: no concluyo que estén caídos.

---

## 5. CDN usadas por servicios populares en Bolivia

Para el ISP 1 usé **Entel (AS6568)** y para el ISP 2 **Tigo (AS27882)**.

| Servicio | ISP boliviano 1 (Entel): evidencia | ISP boliviano 2 (Tigo): evidencia | ¿Caché dentro del ISP demostrada? |
|---|---|---|---|
| Netflix | No verificable (AS2906 no aparece en las listas de vecinos que copié) | No verificable | No |
| Google / YouTube | Relación BGP observada (AS15169 aparece como peer de AS6568). Ambos declaran presencia en Equinix São Paulo en PeeringDB | No verificable (no aparece entre sus vecinos) | No |
| TikTok | No verificable (AS396986 no aparece entre sus vecinos) | No verificable | No |
| Facebook / Meta | Relación BGP observada (AS32934 aparece como peer de AS6568) | No verificable | No |

| CDN comercial | ASN o red encontrada | Servicio / característica identificada |
|---|---|---|
| Cloudflare | AS13335 | Anuncia los prefijos `104.21.0.0/19`, `104.21.16.0/20` y `172.67.144.0/20`, donde caen las IPs de `www.lacascada.com.bo`; además es el servidor de nombres de `lacascada.com.bo` e `impuestos.gob.bo`. En BGP.Tools aparece como peer y downstream de Entel |
| Fastly | AS54113 | Red de tipo Content con 571 prefijos IPv4 y 1168 IPv6 (BGP.Tools). Aparece como peer de Entel |
| Akamai | AS20940 | Akamai International B.V., red de tipo Content registrada en RIPE, con 3920 prefijos IPv4 y 755 IPv6 (BGP.Tools). Aparece como peer de Entel |

**Cloudflare Radar Scanner** sobre `https://www.lacascada.com.bo/`: la solicitud terminó en `https://lacascada.com.bo/` (redirección 301 y luego 200), con 113 solicitudes a 4 dominios y 1 IP. La «IP principal» que reporta es `127.0.1.10`, una dirección de loopback con ASN desconocido, así que ese escaneo **no sirve para ubicar el servidor** (no verificable). Fuente: https://radar.cloudflare.com/scan/4eba56d5-c18d-4225-a0af-c12fbd62bff1. CDN Planet no lo consulté.

**Pregunta final:** hacen falta datos de una fuente específica de cada plataforma (por ejemplo, un programa de caché publicado por el propio servicio o un anuncio del ISP) o ver que el contenido se sirve desde una IP dentro del prefijo del ISP; el peering en un IXP o ser vecino BGP no demuestra que haya servidores de caché instalados. Una IP de CDN no demuestra su ubicación física porque BGP anuncia redes y no lugares, y la misma dirección puede responder desde muchos nodos en distintos países.

---

## Observaciones

1. **`impuestos.bo` no existe, pero el sitio del SIN sí:** `impuestos.bo` devolvió NXDOMAIN en mi resolvedor y en 1.1.1.1, con el SOA de `bo.` como autoridad (dig). El dominio que responde es `impuestos.gob.bo` (IP 170.82.244.41, DNS en Cloudflare), que coincide con el portal que cita el boletín del SIN de mayo de 2026 (https://www.impuestos.gob.bo/wp-content/uploads/2026/06/BOLETIN-MAYO-2026-1.pdf). Ese dominio no responde ICMP (100 % de pérdida); **inferencia:** filtran el ping, no que el sitio esté caído.
2. **Un proveedor común:** Telxius (AS12956) aparece como upstream probable de Entel, AXS, COTAS y Tigo, y también de Netflix, Google, Meta y de un prefijo de Cloudflare (BGP.Tools). **Inferencia:** los ISP que revisé comparten un proveedor de tránsito, pero no es un contrato demostrado.
3. **Entel y las plataformas:** BGP.Tools lista como peers de Entel a Google, Meta, Cloudflare, Amazon, Microsoft, Fastly y Akamai. Con Netflix y TikTok no vi relación. Además de PIT Bolivia, Entel declara presencia en Lima y São Paulo (PeeringDB). Es una **relación BGP observada**, no una caché confirmada.
4. **DNS no revela ubicación:** `tigo.com.bo` resuelve a una IP dentro de un prefijo de AS16509 (Amazon, registrado como US) y tiene un MX hacia Amazon, mientras que `lacascada.com.bo` tiene la raíz y `www` en IPs distintas: la raíz en 69.167.179.79 (nombre inverso `host.web-hosting-bolivia.com`, AS no verificado) y `www` en IPs de Cloudflare. **Inferencia:** el sitio se publica con distintos proveedores, pero con DNS y ping no puedo decir dónde están los servidores.
5. **Límites y diferencias entre fuentes:** el mapa de la ATT no respondió a clics, por lo que no pude contrastar un tramo con la UIT. BGP.Tools usa el nombre comercial («Entel Bolivia») y RDAP la razón social. El escaneo de Cloudflare reportó una IP de loopback. Y mis mediciones salen de una VM con NAT desde La Paz, así que el RTT depende de mi conexión y no equivale a distancia; tampoco usé el `ttl` de las respuestas para contar saltos.

---

## Evidencias de terminal (archivos en la carpeta `lab3` del repositorio)

`dns_impuestos_umsa.txt`, `dns_impuestos_diagnostico.txt`, `dns_aaaa_soa_txt.txt`, `ping_resultados.txt`, `dns_isp.txt`, `ping_isp.txt`, `dns_ping_www_entel.txt`, `dns_ping_lacascada.txt`

## Nota sobre herramientas de IA

Usé Claude como apoyo para guiar la ejecución de los comandos, explicar conceptos y ayudar a redactar este documento a partir de los resultados de mis propias consultas. Los datos provienen de las herramientas indicadas, y las inferencias están marcadas como tales.
