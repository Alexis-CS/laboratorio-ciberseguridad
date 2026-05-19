# Lab 4 — Practica de defensa activa

## Tecnologías y herramientas utilizadas:
- Víctima: Ubuntu Server 18.04 (VM)
- Atacante: Ubuntu Server 24.04 (desde WSL2)
- NMAP
- SSH
- Web NVD (National Vulnerability Database de NIST)
- Unattended-Upgrades: Paquete de software para sistemas basados en Debian/Ubuntu instalado para automatizar la gobernanza de la seguridad del sistema.

## Descripción general
- Actividad: Los alumnos reciben un informe de vulnerabilidades (simulado).
- Tarea: 1. Identificar servicios vulnerables usando nmap --script vuln. 2. Aplicar parches específicos usando apt-get install --only-upgrade [paquete]. 3. Configurar Unattended Upgrades para automatizar parches críticos.
 
 ## Dinámica: 
- Entorno: Una VM Ubuntu Server "congelada" en una versión antigua o con servicios vulnerables (ej. un servicio de impresión o una versión de OpenSSH con vulnerabilidades conocidas).
- Escaneo Inicial: Desde Kali, usar nmap -sV --script vuln [IP_Ubuntu]. Los alumnos deben identificar al menos 3 vulnerabilidades con su código CVE correspondiente.
- Investigación: Buscar en la base de datos del NVD (NIST) qué permite hacer ese CVE (ej. RCE o DoS).
- Aplicación de Parche: Simular la política de "parches críticos primero". Uso de apt-get install --only-upgrade [servicio] para no romper dependencias globales.
- Configuración de Seguridad Autónoma: Instalar unattended-upgrades. 1sudo apt install unattended-upgrades y Configurar /etc/apt/apt.conf.d/50unattended-upgrades para que solo se instalen parches de "Security".

Habilidad ganada: Capacidad de priorizar la remediación basada en el impacto organizacional.

## Pasos del laboratorio

### Escaneo a la máquina víctima

<img width="912" height="265" alt="Escaneo de la maquina vulnerable" src="https://github.com/user-attachments/assets/53cd5b0c-a777-4308-9913-63025ca9e038" />

Se utilizó el motor de scripts de Nmap para detectar servicios desactualizados y sus vulnerabilidades (otorgandonos la CVE y el nivel crítico que poseen estas).

<img width="1810" height="480" alt="ssh versión y CVE" src="https://github.com/user-attachments/assets/3f17955a-7f20-4241-bd06-64e4ae88c30c" />

En este caso como se puede ver, la versión del servicio ssh esta desactualizada y contiene múltiples vulnerabilidades de alto riesgo.

### Investigación

<img width="1781" height="962" alt="Investigación1" src="https://github.com/user-attachments/assets/019915b7-51f7-49f1-bb95-16123625998b" />

**CVE-2023-38408:** Fallo en la característica PKCS#11 de ssh-agent. Permite la ejecución remota de código arbitrario si el agente es reenviado a un sistema controlado por el atacante.

<img width="1777" height="802" alt="Investigación2" src="https://github.com/user-attachments/assets/465933f5-aec8-453a-9bb8-20333fc5ba9b" />

**CVE-2026-35414:** Mal manejo de la opción authorized_keys principals en escenarios que involucran caracteres de coma junto a una Entidad Certificadora.

<img width="1797" height="777" alt="imagen" src="https://github.com/user-attachments/assets/b9919298-a1a9-454c-8585-3972fcb7487d" />

Toda esta información permite a un atacante remoto reconocer que tipo de vulnerabilidades tiene el servicio y de paso la mejor forma de comprometerlo. Todo esta información se extrae de la siguiente fuente pública:

https://nvd.nist.gov/

### Aplicación de Controles (Parchado)

<img width="806" height="286" alt="Captura de pantalla 2026-05-18 194948" src="https://github.com/user-attachments/assets/fc04723b-d1b5-43a8-88e2-9754504d670a" />

<img width="791" height="363" alt="Captura de pantalla 2026-05-18 195227" src="https://github.com/user-attachments/assets/5b267e3c-86bc-4dbc-8293-e3fe54d5ae3e" />

Siguiendo la política organizacional de "parches críticos primero" y con el fin de mitigar el riesgo, se ejecuta la actualización exclusiva del servicio afectado.

<img width="1097" height="397" alt="imagen" src="https://github.com/user-attachments/assets/8ca04567-70ba-41f5-80d2-3c410d4d3af5" />

Realizando un nuevo escaneo, podemos verificar que el servicio ssh se ha actualizado, y se puede verificar comparando la imagen anterior con la del primer escaneo.

### Configuración de Seguridad Autónoma (Hardening)

<img width="802" height="602" alt="imagen" src="https://github.com/user-attachments/assets/6e3d9d38-62d5-41a7-9067-6c21870f208f" />

Para evitar tener que parchar manualmente cada vulnerabilidad que aparezca en el futuro, configuraremos el sistema para que se defienda solo ante amenazas críticas. Primero se debe instalar el paquete de actualizaciones desatendidas.

<img width="807" height="131" alt="Captura de pantalla 2026-05-19 021910" src="https://github.com/user-attachments/assets/6d26ec92-3c2e-49ad-8355-8b4de96c057f" />

Este comando "sudo dpkg-reconfigure -plow unattended-upgrades" sirve para activar, desactivar o modificar las actualizaciones automáticas de seguridad en tu sistema Linux (Debian/Ubuntu).

<img width="797" height="600" alt="imagen" src="https://github.com/user-attachments/assets/564d3cd5-4816-4889-a9f8-5475c166bbd6" />

Al ejecutarlo, el sistema abrirá un menú interactivo en la terminal que te preguntará si deseas que el servidor descargue e instale los parches de seguridad de forma automática y en segundo plano, sin que tú tengas que intervenir.

# Conclusión

Aprendizaje

El laboratorio demostró que la seguridad de un sistema no es un estado estático, sino un proceso continuo. El aprendizaje clave fue la transición de una postura de defensa reactiva (auditar vulnerabilidades con Nmap) a una defensa proactiva y autónoma mediante la configuración precisa de unattended-upgrades. Se comprendió la importancia de aplicar políticas de parcheo (--only-upgrade) en entornos de producción para mitigar riesgos críticos sin comprometer la disponibilidad ni la estabilidad operativa de la infraestructura.

Al restringir las actualizaciones automáticas exclusivamente al repositorio de seguridad (-security) y parchar los servicios expuestos, se neutralizan directamente las siguientes amenazas:
- Ejecución Remota de Código (RCE): Bloquea exploits críticos (como el detectado CVE-2023-38408) que permiten a un atacante tomar el control total del servidor a distancia.
- Denegación de Servicio (DoS): Parchea fallos estructurales que los atacantes aprovechan para desestabilizar o botar los servicios de la organización.
- Enumeración y Explotación de Servicios: Evita el reconocimiento exitoso de versiones de software con fallos públicos conocidos, deteniendo la cadena del ataque en su fase inicial.
