# Lab 5 — implementación de IDS

## Tecnologías y herramientas utilizadas:
- Víctima: Ubuntu Server 18.04 (VM)
- Atacante: Ubuntu Server 24.04 (desde WSL2)
- SSH
- Fail2Ban: La herramienta principal que analiza logs y bloquea IPs.
- Hydra
- Iptables/Nftables: El firewall interno de Linux que ejecuta el bloqueo físico.

## Descripción general
- Actividad: Configurar un sistema de detección y respuesta automática.
- Tarea: 1-Instalar Fail2Ban para proteger el servicio SSH. 2-Lanzar un ataque de fuerza bruta desde Kali con hydra. 3-Verificar en los logs cómo el IDS detecta el ataque y bloquea la IP del atacante automáticamente en el firewall (iptables).

## Pasos del laboratorio

### Instalación de fail2ban

<img width="805" height="617" alt="imagen" src="https://github.com/user-attachments/assets/2a4d477e-5f7f-4700-95bb-ec4a5dee0e6c" />

Primero instalamos fail2ban, quien actúa como el "guardaespaldas" en nuestro servidor.

### Configuración de la "Cárcel" (Jail)

Fail2Ban usa "cárceles" para decidir a quién bloquear. Vamos a configurar la de SSH.

<img width="687" height="120" alt="imagen" src="https://github.com/user-attachments/assets/654db787-c3ab-4267-8f95-4300d768e713" />

Creamos el archivo de configuración local y con **sudo nano /etc/fail2ban/jail.local** editamos el archivo

<img width="801" height="597" alt="imagen" src="https://github.com/user-attachments/assets/a0968a62-2d64-4c3a-8331-60ecb6aa2de2" />

enabled = true: Inicializa el módulo de escucha para este servicio.

maxretry = 3: Configura el límite de tolerancia en 3 intentos fallidos de autenticación.

findtime = 600: Ventana de tiempo (10 minutos) en la cual se contabilizan los intentos fallidos.

bantime = 600: Tiempo de aislamiento físico (10 minutos) en el cortafuegos para la IP infractora.

<img width="747" height="322" alt="imagen" src="https://github.com/user-attachments/assets/529b1542-57fb-4cec-b444-978d77652aec" />

Reiniciamos el Fail2Ban para asentar la nueva configuración.

### Preparar la vigilancia

<img width="797" height="287" alt="imagen" src="https://github.com/user-attachments/assets/39b6fea9-61db-4846-9446-ff3682500a35" />

Para verificar la telemetría del IDS en tiempo real, dejamos la terminal del servidor imprimiendo las últimas líneas del log para ver cuándo detecta al atacante.

### El ataque

<img width="1872" height="251" alt="imagen" src="https://github.com/user-attachments/assets/4d30420b-c9a2-451f-a85f-e3f90ffe78cf" />

Ahora, actúa como el atacante. Vamos a usar Hydra para intentar adivinar la clave de nuestro Ubuntu de forma masiva.

### Observar la Reacción (En el Ubuntu víctima)

<img width="652" height="492" alt="imagen" src="https://github.com/user-attachments/assets/5f02e713-0a82-43a7-9a7b-8bb4717a8e78" />

<img width="426" height="487" alt="imagen" src="https://github.com/user-attachments/assets/0858a1c6-c860-4f2e-993a-710dbcedfc6f" />

Como se ve en la imagen, el IDS detectó la ráfaga de intentos fallidos y dio la orden de bloqueo.

<img width="1882" height="267" alt="imagen" src="https://github.com/user-attachments/assets/f5f83ad4-6508-4ca0-b792-a04b5ab47408" />

Ahora no se me permite realizar un ataque con hydra desde la parte del atacante, rechazando la conexión.

### Verificación en el Firewall

<img width="785" height="282" alt="imagen" src="https://github.com/user-attachments/assets/bb81ebea-85e8-48e2-86c3-088242b23240" />

Colocando el comando "sudo iptables -L -n" verás que el bloqueo es real y el atacante ya no puede ni siquiera intentar conectarse. En la regla que dice REJECT se verá la IP del atacante detectado por el IDS. 

# Conclusión

Aprendizaje

Este laboratorio enseña a implementar Defensa Activa. Ya no solo tenemos una puerta fuerte (el parche del Lab 1), sino que ahora tenemos un sistema inteligente que reconoce el comportamiento de un atacante y responde automáticamente sin que el administrador tenga que mover un dedo.

Al aplicar este control técnico perimetral, el servidor Ubuntu Server bloquea de forma autónoma los siguientes vectores de ataque en las capas de transporte y aplicación:
- Ataques de Fuerza Bruta Masivos (Brute Force Attacks)
- Ataques de Diccionario (Dictionary Attacks)
- Secuestro de Sesiones y Credenciales por Reutilización (Credential Stuffing)
- Escaneo de Puertos y Reconocimiento Agresivo (Port Scanning / Footprinting)
