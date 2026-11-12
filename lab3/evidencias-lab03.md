# Evidencias — Laboratorio 3: Enlaces Internacionales de Internet en Bolivia

**Estudiante:** Mauricio Huallpa
**Fecha de las consultas:** 10 de noviembre de 2026 (hora de Bolivia)

Cada captura corrobora una página consultada para el documento `Laboratorio_Enlaces_Internacionales_Huallpa_Mauricio.md`. Las imágenes están en la carpeta `evidencias/` y se enlazan por nombre.

---

## Parte 1 — Fibra

### 01. UIT Broadband Maps: tramo La Paz – Patacamaya
Página: https://bbmaps.itu.int/bbmaps/
Debe verse: operador (Empresa Nacional de Telecomunicaciones S.A.), extremos La Paz y Patacamaya, distancia 90.31 y estado Operational.

![UIT La Paz - Patacamaya](evidencias/01-uit-patacamaya.png)

### 02. UIT Broadband Maps: tramo La Paz – Calamarca
Página: https://bbmaps.itu.int/bbmaps/
Debe verse: operador (Millicom Bolivia), extremos La Paz y Calamarca, distancia 56.84 y estado Operational.

![UIT La Paz - Calamarca](evidencias/02-uit-calamarca.png)

### 03. ATT SMI: documento consultado
Página: https://smi.att.gob.bo/
Debe verse: el título «Estado de situación de las telecomunicaciones en Bolivia, primer semestre gestión 2025».

![ATT documento](evidencias/03-att-documento.png)

### 04. ATT SMI: mapa de fibra, radiobases y cobertura (opcional)
Página: https://smi.att.gob.bo/
Debe verse: el mapa publicado, que en mi navegador no respondió a clics (por eso el contraste de un tramo con la UIT es «no verificable»).

![ATT mapa](evidencias/04-att-mapa.png)

### 05. Submarine Cable Map: South America-1 (SAm-1)
Página: https://www.submarinecablemap.com/
Debe verse: nombre, fecha de servicio (marzo de 2001), longitud (25,000 km), propietario Telxius y la estación de amarre usada (Arica, Chile).

![SAm-1](evidencias/05-cable-sam1.png)

### 06. Submarine Cable Map: South Pacific Cable System (SPCS) / Mistral
Página: https://www.submarinecablemap.com/
Debe verse: nombre, fecha de servicio (agosto de 2021), longitud (7,300 km), propietarios América Móvil (Claro) y Telxius, y el punto de amarre Arica, Chile.

![SPCS Mistral](evidencias/06-cable-spcs-mistral.png)

---

## Parte 2 — ASN y proveedores

Cada captura de BGP.Tools muestra la parte superior de la ficha: nombre, número de AS, tipo de red y upstreams.

### 07. ENTEL — AS6568
Página: https://bgp.tools/as/6568

![AS6568 Entel](evidencias/07-bgptools-as6568-entel.png)

### 08. TIGO — AS27882
Página: https://bgp.tools/as/27882

![AS27882 Tigo](evidencias/08-bgptools-as27882-tigo.png)

### 09. AXS — AS26210
Página: https://bgp.tools/as/26210

![AS26210 AXS](evidencias/09-bgptools-as26210-axs.png)

### 10. COTAS — AS25620
Página: https://bgp.tools/as/25620

![AS25620 COTAS](evidencias/10-bgptools-as25620-cotas.png)

### 11. COMTECO — AS27839
Página: https://bgp.tools/as/27839
Debe verse además que AS6568 (Entel) aparece como upstream.

![AS27839 Comteco](evidencias/11-bgptools-as27839-comteco.png)

### 12. Google — AS15169
Página: https://bgp.tools/as/15169

![AS15169 Google](evidencias/12-bgptools-as15169-google.png)

### 13. Netflix — AS2906
Página: https://bgp.tools/as/2906

![AS2906 Netflix](evidencias/13-bgptools-as2906-netflix.png)

### 14. Meta / Facebook — AS32934
Página: https://bgp.tools/as/32934

![AS32934 Meta](evidencias/14-bgptools-as32934-meta.png)

### 15. TikTok / ByteDance — AS396986
Página: https://bgp.tools/as/396986

![AS396986 ByteDance](evidencias/15-bgptools-as396986-tiktok.png)

### 16. LACNIC RDAP — AS6568
Página: https://rdap.lacnic.net/rdap/autnum/AS6568
Debe verse: el número AS, el tipo de asignación y el titular (Empresa Nacional de Telecomunicaciones Sociedad Anónima). Se omite la sección de contactos.

![RDAP AS6568](evidencias/16-rdap-as6568.png)

### 17. LACNIC RDAP — AS27882
Página: https://rdap.lacnic.net/rdap/autnum/AS27882
Debe verse: el número AS y el titular (Telefónica Celular de Bolivia S.A.). Se omite la sección de contactos.

![RDAP AS27882](evidencias/17-rdap-as27882.png)

---

## Parte 3 — Peering, tránsito y BGP

### 18. BGP.Tools: vecinos de Entel (Connectivity)
Página: https://bgp.tools/as/6568
Debe verse: la lista de upstreams (por ejemplo Lumen y Telxius) y la de peers, con Google en ella.

![Connectivity Entel](evidencias/18-bgptools-entel-connectivity.png)

### 19. BGP.Tools: IXP de Entel
Página: https://bgp.tools/as/6568
Debe verse: PIT Bolivia, PIT Perú (Lima) y Equinix São Paulo.

![IX Entel](evidencias/19-bgptools-entel-ix.png)

### 20. BGP.Tools: IXP de Tigo (opcional)
Página: https://bgp.tools/as/27882
Debe verse: PIT Bolivia.

![IX Tigo](evidencias/20-bgptools-tigo-ix.png)

### 21. PeeringDB: Entel
Página: https://www.peeringdb.com/org/16536
Debe verse: ASN 6568, política de peering (Selectivo) y requisitos de contrato y ratio.

![PeeringDB Entel](evidencias/21-peeringdb-entel.png)

### 22. PeeringDB: Google
Página: https://www.peeringdb.com/org/574
Debe verse: ASN 15169 y política de peering (Selectivo). Se añade otra captura de la lista de puntos de intercambio con la fila de Equinix São Paulo.

![PeeringDB Google](evidencias/22-peeringdb-google.png)

![PeeringDB Google IXP](evidencias/22b-peeringdb-google-ixp.png)

### 23. RIPEstat: prefijo 190.129.48.0/20
Página: https://stat.ripe.net/
Debe verse: el prefijo, que lo anuncia AS6568, la visibilidad (99.4 % de 320 peers) y la fecha de consulta.

![RIPEstat](evidencias/23-ripestat-prefijo.png)

---

## Parte 4 — DNS

### 24. Google Admin Toolbox Dig: umsa.bo (MX)
Página: https://toolbox.googleapps.com/apps/dig/
Debe verse: los 5 registros MX de Google con sus preferencias y TTL de 1 hora, para comparar con la consulta local.

![Google Dig umsa.bo](evidencias/24-google-dig-umsa-mx.png)

---

## Parte 5 — CDN

### 25. BGP.Tools: búsqueda de la IP 190.129.60.166 (www.entel.bo)
Debe verse: los prefijos superpuestos `190.129.48.0/20`, `190.129.0.0/17` y `190.129.0.0/16`, todos originados por AS6568.

![IP Entel](evidencias/25-bgptools-ip-190-129-60-166.png)

### 26. BGP.Tools: búsqueda de la IP 52.0.229.131 (tigo.com.bo)
Debe verse: los prefijos `52.0.0.0/15` (AS14618) y `52.0.0.0/11` (AS16509), ambos con país US.

![IP Tigo](evidencias/26-bgptools-ip-52-0-229-131.png)

### 27. BGP.Tools: IP de www.lacascada.com.bo
Debe verse: la búsqueda de `104.21.28.182` (prefijos `104.21.0.0/19` y `104.21.16.0/20`, AS13335) o la ficha del prefijo `172.67.144.0/20` (originado por AS13335, Cloudflare, Inc.).

![Prefijo Cloudflare](evidencias/27-bgptools-prefijo-cloudflare.png)

### 28. Cloudflare Radar Scanner: lacascada.com.bo
Página: https://radar.cloudflare.com/scan/4eba56d5-c18d-4225-a0af-c12fbd62bff1
Debe verse: la URL enviada, la redirección 301 → 200, el resumen con la IP principal `127.0.1.10` y la fecha del reporte.

![Cloudflare Radar](evidencias/28-radar-lacascada.png)

### 29. BGP.Tools: Fastly — AS54113
Página: https://bgp.tools/as/54113

![AS54113 Fastly](evidencias/29-bgptools-as54113-fastly.png)

### 30. BGP.Tools: Akamai — AS20940
Página: https://bgp.tools/as/20940

![AS20940 Akamai](evidencias/30-bgptools-as20940-akamai.png)

---

## Evidencias de terminal (archivos de texto en esta misma carpeta)

Las consultas con `dig` y `ping` quedaron guardadas como texto, con la fecha al inicio de cada archivo.

| Archivo | Qué corrobora |
|---|---|
| [dns_impuestos_umsa.txt](dns_impuestos_umsa.txt) | A, NS y MX de `impuestos.bo` (sin respuesta) y `umsa.bo` |
| [dns_impuestos_diagnostico.txt](dns_impuestos_diagnostico.txt) | NXDOMAIN de `impuestos.bo` en el resolvedor local y en 1.1.1.1; registros de `impuestos.gob.bo` |
| [dns_aaaa_soa_txt.txt](dns_aaaa_soa_txt.txt) | AAAA, SOA y TXT de `umsa.bo` e `impuestos.gob.bo` |
| [ping_resultados.txt](ping_resultados.txt) | `ping` a `impuestos.bo`, `umsa.bo` e `impuestos.gob.bo` |
| [dns_isp.txt](dns_isp.txt) | A, NS y MX de `entel.bo` y `tigo.com.bo` |
| [ping_isp.txt](ping_isp.txt) | `ping` a `entel.bo` y `tigo.com.bo` |
| [dns_ping_www_entel.txt](dns_ping_www_entel.txt) | Estado de `entel.bo` (NOERROR sin registro A), `www.entel.bo` y su `ping` |
| [dns_ping_lacascada.txt](dns_ping_lacascada.txt) | A, NS y MX de `lacascada.com.bo`, registro A de `www` y `ping` a ambos |
