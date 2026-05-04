# Promt usado para la creacion de la plataforma 

Crear una plataforma WEB, dame el paso a paso que se debería tener para la creación de cada modulo, luego redacta un párrafo por cada uno con el fin de crear un documento de Word de apoyo para la implementación de la plataforma para que  sirva de apoyo para un ingeniero en telecomunicaciones, con los los siguientes módulos

-	Modulo 1: Plantillas multi plataformas para configuración de SW,Router, Firewall, de marcas como Cisco, Huawi,Aruba, HP ,Palo alto entre otros, que me permitan hacer filtrado de las configuraciones básicas y modificables con el fin de que se puedan cargar directamente a los dispositivos o en su defecto tener plantillas con el paso a paso según la configuración que escoja.
  
-	Modulo 1:Debe tener un dashboards que me permita monitorear en tiempo real (ping/SNMP simulado), rendimiento (ancho de banda, jitter pérdida de paquetes) y eventos logs umbrales superados, todo a nivel de RED para tener la mejor calidad en el servicio, adicional que me permita crear widget para tener más detalles simulando herramientas similares a wireshark, SolarWind entre otros.
  
-	Modulo 3: Debe tener un apartado que se especialice en trafico de voz y video, que me permita tener métricas en tiempo real y análisis con software especializadas para este campo, con el cual me puede anticipar a colapso de la red.
  
-	Modulo 4: Debe tener un apartado especializado en capa 2 y capa de 3 del modelo OSI con el cual me permita crear configuraciones automáticas para SW en caso de fallas inesperadas o configuraciones masivas de equipos.
  
-	Modulo 5: Debe tener un apartado que se especialice en  ACLs Permitir/denegar tráfico según IP, protocolo (UDP/ y puertos específicos (ej solo permitir SIP/RTP a VLAN voice
  
- Todos los módulos deben tener sistema de alerta según los parámetros que se configuren de manera manual o tomando como base los análisis del dahsboard  

Teniendo en cuenta el promt inicial, crear un paso a paso mas técnico para de cómo se crearía la plataforma WEB, dale una estructura mas real de como serían los módulos y las configuraciones de cada uno, luego redacta un párrafo por cada uno con el fin de crear un documento de Word para presentación de proyecto ante jefes de una área de teleco

# Resultado 

## Stack tecnológico propuesto

<img width="1060" height="603" alt="image" src="https://github.com/user-attachments/assets/4a78e775-7c19-4418-9acc-5d72840034bb" />

###
Principios de diseño 
• Arquitectura modular desacoplada: cada módulo expone su propia API REST interna.

• Multi-vendor abstraction layer: capa de abstracción que normaliza comandos para Cisco
IOS/NX-OS, Huawei VRP, Aruba AOS-CX, HP Comware y Palo Alto PAN-OS.

• Event-driven alerts: motor de reglas basado en umbrales configurables con
disparadores en tiempo real.

• Audit trail completo: toda acción de configuración queda registrada con timestamp,
usuario y diff del cambio.

• Seguridad por capas: cifrado en tránsito (TLS 1.3), cifrado en reposo, segmentación de
roles y MFA opcional.


## Modulo 1 Catálogo inicial de plantillas por fabricante

### Fase 1 Motor de plantillas (Backend)

1. Diseñar el modelo de datos para plantillas: fabricante, línea de producto, versión de
sistema operativo, tipo de configuración (VLAN, OSPF, BGP, NAT, QoS, etc.).

2. Implementar el repositorio de plantillas utilizando Jinja2 como motor de renderizado.
Cada plantilla contiene variables dinámicas (nombre de interfaz, VLAN ID, dirección IP,
máscara, etc.) definidas en un esquema JSON/YAML.

3. Desarrollar el driver multi-vendor usando la librería NAPALM para las operaciones
get/set y Netmiko para acceso SSH directo. Mapear cada fabricante a su driver

correspondiente: cisco_ios, cisco_nxos, huawei_vrp, aruba_aoscx, paloalto_panos.
4. Crear el endpoint REST POST /api/v1/templates/render que recibe el ID de plantilla y
los valores de los parámetros, renderiza la configuración final y retorna el diff previo al
envío.

5. Implementar el endpoint POST /api/v1/templates/deploy que ejecuta la configuración
sobre el dispositivo, captura la salida del CLI y registra el resultado en la base de datos
de auditoría.

### Fase 2 nterfaz de usuario

6. Desarrollar el wizard de selección de plantilla: selector de fabricante → familia de
producto → versión SO → tipo de configuración.

7. Renderizar formulario dinámico con los campos requeridos por la plantilla seleccionada
(generado automáticamente desde el esquema JSON de la plantilla).

8. Implementar el panel de previsualización con diff coloreado (verde = adiciones, rojo =
eliminaciones) antes del despliegue.

9. Agregar modo paso a paso: al activarlo, la plataforma presenta cada bloque de
comandos de forma secuencial con explicación de cada instrucción, orientado a
capacitación o procedimientos guiados.

<img width="791" height="341" alt="image" src="https://github.com/user-attachments/assets/47c2373b-867c-420f-aa55-9286a80e0873" />


## Modulo 2 Dashboard de monitoreo en tiemo real

### Fase 1 — Recolección de métricas
1-  Implementar el agente de ping activo usando ICMP con intervalos configurables (default
30 s). Registrar latencia, jitter y pérdida de paquetes en InfluxDB con tags de
dispositivo, sitio y criticidad.

2- Desarrollar el colector SNMP con Net-SNMP o pysnmp, programando el poleo de MIBs
clave: IF-MIB (utilización de interfaces), HOST-RESOURCES-MIB (CPU/RAM), CISCOMEMORY-
POOL-MIB, y MIBs propietarias por fabricante.

3-  Crear el pipeline de ingesta: colector → Prometheus (scraping cada 15 s) → InfluxDB /
TimescaleDB para retención histórica. Configurar reglas de alerta en Prometheus
Alertmanager.

4-   Integrar recepción de traps SNMP v2c/v3 en un listener UDP:162 que normaliza los
eventos y los almacena en la tabla de eventos con severidad (Critical, Major, Minor,
Warning, Info).

### Fase 2 — Motor de widgets y visualización
 5- Desarrollar el sistema de widgets con React + react-grid-layout para arrastre y
redimensionado. Cada widget es un componente independiente que suscribe a un canal
WebSocket específico.

6-  Implementar los widgets base: Mapa topológico de red (vis.js / D3.js), Gráfica de
utilización de ancho de banda por interfaz, Gauge de latencia/jitter/pérdida, Tabla de
eventos en vivo con filtros, Heatmap de CPU/RAM por dispositivo, Timeline de
disponibilidad (uptime histórico).

7-  Agregar widget de captura simulada tipo Wireshark: decodificación de protocolos a partir
de datos SNMP y NetFlow para mostrar top talkers, top aplicaciones y distribución de
protocolos por puerto.

8- Implementar exportación de reportes en PDF y CSV desde cualquier widget con rango
de fechas configurable.

<img width="786" height="267" alt="image" src="https://github.com/user-attachments/assets/6915b2c0-d369-4a16-b0b1-ae8fbea53f0d" />

## Modulo 3 Analisis de trafico de voz y video

### Fase 1 — Captura y decodificación de protocolos VoIP/Video

1-  Desplegar un agente de captura pasiva en puntos estratégicos de la red (mirror port /
SPAN) usando libpcap/tshark como backend. El agente filtra y extrae únicamente tráfico
SIP (UDP/TCP 5060, 5061) y RTP (puertos dinámicos negociados en SDP).

2-  Implementar el parser SIP que extrae: método de la solicitud (INVITE, BYE,
REGISTER), código de respuesta, Call-ID, duración de sesión, codec negociado
(G.711, G.729, OPUS, H.264, H.265).

3-  Desarrollar el analizador RTP/RTCP que calcula en tiempo real: jitter (RFC 3550),
pérdida de paquetes, MOS estimado (ITU-T E-Model), SSRC tracking para correlación
de flujos.

4-  Para video: parsear cabeceras RTP de streams H.264/H.265 para extraer resolución,
tasa de fotogramas estimada, pérdida de I-frames y rebuffering events.

### Fase 2 — Motor de predicción de colapso


5-  Implementar análisis de tendencias con ventana deslizante de 5 minutos sobre las
métricas MOS y jitter. Si la tendencia supera el umbral de degradación proyectada
(MOS < 3.5 en los próximos 2 min), disparar alerta predictiva.

6-  Desarrollar el mapa de flujos activos de voz/video: visualización en tiempo real de todas
las sesiones activas con su calidad asociada (verde > 4.0 MOS, amarillo 3.0-4.0, rojo <
3.0).

7-  Integrar correlación automática con eventos de red del Módulo 2: si una sesión de voz
se degrada en el mismo instante que un evento SNMP de congestión de interfaz, el
sistema correlaciona ambos eventos en un único ticket de incidente.

<img width="791" height="298" alt="image" src="https://github.com/user-attachments/assets/65b84b44-86af-4b38-9a26-30dd7899a6c6" />

# Modulo 4 Gestión automatizada de capa 2 y 3 

### Fase 1 — Descubrimiento y modelado de topología

1-  Implementar descubrimiento automático de topología mediante LLDP y CDP polling vía
SNMP. Construir el grafo de red con NetworkX (Python) almacenando nodos
(dispositivos), aristas (enlaces), VLAN memberships y relaciones de trunk.

2-  Desarrollar la visualización interactiva de topología en el frontend usando D3.js o
Cytoscape.js, con capas separadas para Capa 2 (VLANs, STP, LAG) y Capa 3
(subredes, rutas, protocolos de routing).

3-  Sincronizar el modelo de topología cada 5 minutos y detectar cambios de estado de
interfaces (up/down) en tiempo real mediante traps SNMP linkUp/linkDown.

### Fase 2 — Automatización de configuración masiva

4-  Desarrollar el motor de políticas de configuración masiva: el ingeniero define una
política (ejemplo: desplegar VLAN 200 en todos los switches del piso 3) y el sistema
genera y ejecuta los comandos en todos los dispositivos afectados de forma paralela
usando Nornir con workers concurrentes.

5-  Implementar rollback automático: antes de aplicar cualquier cambio masivo, el sistema
toma un snapshot de la configuración actual. Si el cambio resulta en pérdida de
conectividad (detectada por ping post-deploy), se ejecuta el rollback automáticamente
en los dispositivos afectados.

6-  Crear flujos de trabajo de recuperación automática ante fallas de STP (bucles
detectados por BPDU storm), portfast inconsistencies y root bridge changes

<img width="773" height="260" alt="image" src="https://github.com/user-attachments/assets/2cca493e-0c20-4858-8d89-7e4cc676a002" />

Modulo 5 Gestión avanzada de ACLs

### Fase 1 — Motor de políticas de ACL

1-  Diseñar el modelo de datos abstracto de regla ACL: acción (permit/deny), protocolo (IP,
TCP, UDP, ICMP, SIP, RTP), IP origen (host, red, cualquiera), IP destino, puerto origen,
puerto destino, dirección (inbound/outbound), interfaz de aplicación, VLAN objetivo.

2-  Implementar el compilador de reglas que traduce el modelo abstracto al lenguaje nativo
de cada fabricante: access-list para Cisco IOS, acl para Huawei VRP, ip access-list para
Aruba AOS-CX, security policy para Palo Alto PAN-OS.

3-  Desarrollar el validador de reglas que detecta: reglas redundantes, reglas sombra
(shadowed rules que nunca serán alcanzadas), conflictos entre reglas y gaps de
cobertura.

4-  Crear el endpoint POST /api/v1/acl/deploy que ejecuta las ACLs en los dispositivos
seleccionados con confirmación de aplicación y registro de auditoría.

### Fase 2 — Casos de uso especializados

5-  Implementar el asistente de ACL para VoIP: permite al ingeniero seleccionar la VLAN de
voz y el sistema genera automáticamente las reglas para permitir SIP (UDP 5060/5061),
RTP (rango dinámico configurable, default 16384-32767) y RTCP, mientras deniega el
resto del tráfico no autorizado hacia esa VLAN.

6-  Desarrollar la vista de matriz de conectividad: visualización bidimensional que muestra
qué VLANs/subredes pueden comunicarse entre sí según las ACLs vigentes, facilitando
auditorías de segmentación.

7-  Integrar análisis de hit count: mediante SNMP o polling CLI, el sistema obtiene los
contadores de matches de cada entrada ACL y alerta sobre reglas con cero hits en los
últimos N días (candidatas a eliminación) o con picos inusuales de tráfico denegado.

<img width="788" height="305" alt="image" src="https://github.com/user-attachments/assets/5a05849f-e2f4-43e6-b424-a60c4fbadeb9" />

## Sistema transversal de alertas

Ees un componente transversal que opera de forma
integrada con todos los módulos de la plataforma. Su diseño está basado en un motor de
reglas configurable que evalúa continuamente las métricas recolectadas por los agentes de
monitoreo y dispara notificaciones cuando se superan los umbrales definidos, ya sea de forma
manual por el operador o a partir de las recomendaciones generadas automáticamente por el
análisis de línea base del dashboard. Las alertas se clasifican por severidad, se correlacionan
entre módulos para reducir el ruido operacional y se enrutan hacia los canales de notificación
seleccionados por el equipo de telecomunicaciones.

### Canales de notificación soportados

- Correo electrónico (SMTP): con plantilla HTML configurable por severidad.
- SMS / WhatsApp Business API: para alertas Critical y Major.
- Webhook genérico: integración con plataformas como Slack, Microsoft Teams,
PagerDuty, ServiceNow.
- Panel de notificaciones en la plataforma: centro de notificaciones en tiempo real via
WebSocket.
- Ticketing automático: creación de incidente en el sistema ITSM de la organización vía
REST API.

<img width="796" height="355" alt="image" src="https://github.com/user-attachments/assets/a3a98c42-a36b-4094-989d-02c2366c08b9" />

# Plan de implementación 

<img width="794" height="454" alt="image" src="https://github.com/user-attachments/assets/b4c21a78-104c-4e20-b18e-2440f6847073" />

