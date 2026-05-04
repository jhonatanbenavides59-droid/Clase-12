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

Fase 1 — Motor de plantillas (Backend)
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
Fase 2 — Interfaz de usuario
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
