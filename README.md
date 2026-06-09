# laboratorio-ciberseguridad

Bienvenido a mi repositorio de **Seguridad de Sistemas**. Este espacio reúne el desarrollo técnico, las metodologías de análisis y las evidencias de ingeniería correspondientes a los laboratorios prácticos de la asignatura, enfocados en la auditoría de vulnerabilidades, la mitigación de riesgos perimetrales y la automatización de la defensa activa.

# 🗺️ Mapa de Ruta del Repositorio

El repositorio se encuentra estructurado en base a dos grandes unidades de aprendizaje técnico:

El contenido técnico está dividido en dos grandes bloques que abarcan desde la protección de la información en tránsito hasta el endurecimiento autónomo del Kernel del sistema.

### 🔐 Bloque I: Criptografía Aplicada, Gestión de Identidades y Tráfico Seguro (Parte 1)
Este bloque se enfoca en asegurar la confidencialidad, integridad y el no repudio de la información, además de robustecer los perímetros de autenticación de cara al usuario.

* **Laboratorio 1: Criptografía de Llave Pública e Integridad de Archivos:** Implementación de funciones de una sola vía (**Hashing SHA-256**) y uso de **OpenSSL** para la generación de llaves asimétricas y firmas digitales normativas, mitigando la alteración no autorizada de manifiestos de configuración críticos.
* **Laboratorio 2: Simulación de Ataque a Credenciales y Despliegue de MFA:** Ejecución de auditorías de contraseñas mediante ataques automatizados de fuerza bruta y diccionario (**Hydra/John the Ripper**), seguido del endurecimiento del módulo PAM con **Google Authenticator** para neutralizar el secuestro de sesiones por contraseñas débiles.

### 🛡️ Bloque II: Hardening del Sistema, IDS y Resiliencia de Infraestructura (Parte 2)
Este bloque se enfoca en la defensa proactiva del servidor a nivel de sistema operativo, el análisis predictivo de logs y la automatización de la contención ante incidentes de seguridad.

* **Laboratorio 3: Gestión de Parches Quirúrgicos y Hardening Activo:** Mapeo automatizado de vulnerabilidades mediante scripts avanzados de **Nmap (`--script vuln`)**, correlación bajo estándares internacionales **CVE (NVD/NIST)** y automatización de remediación autónoma mediante el módulo `unattended-upgrades` restringido a repositorios críticos de seguridad.
* **Laboratorio 4: Implementación de HIDS/IPS Operacional con Fail2Ban:** Configuración de un Sistema de Prevención de Intrusos perimetral para aislar de forma automática a atacantes concurrentes, inyectando de manera dinámica reglas de bloqueo de tipo `REJECT` directas en el Firewall nativo del sistema (**IPTables/Netfilter**).

## 🛠️ Stack Tecnológico Global Utilizado

| Capa Ofensiva (Ubuntu Server 24.04/WSL) | Capa Defensiva (Ubuntu Server 18.04) |
| :--- | :--- | :--- |
| 🦅 **Hydra v9.5** (Fuerza Bruta) | 🛡️ **Fail2Ban** (HIDS / IPS) | 🔑 **AES-256 / RSA-2048** |
| 🔍 **Nmap** (Análisis de CVEs) | 🔥 **IPTables** (Netfilter Firewall) | 🔒 **SSH** |
| 📦 **APT / Dpkg** (Package Hardening) | 📑 **CVE (MITRE / NIST NVD)** |
| 📁 **Wordlists / RockYou.txt** |

---
