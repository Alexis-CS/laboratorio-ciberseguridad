# Laboratorio 2 — Ataque y Defensa de Credenciales

## Tecnologías y herramientas utilizadas:
- Atacante: Ubuntu Server 24.04 (desde WSL2)
- Víctima: Ubuntu Server 18.04 (VM)
- SSH
- Hydra
- Google Authenticator
- PAM (Pluggable Authentication Modules)
- Diccionario RockYou

## Descripción general

- Actividad: Realizar ataques de diccionario y fuerza bruta, para luego implementar MFA.
- Herramientas: Hydra, John the Ripper, Google Authenticator PAM module.
- Tarea: Romper una contraseña débil de SSH usando Hydra.

Luego, instalar y configurar el módulo de Google Authenticator en el servidor Ubuntu para que, aunque el atacante tenga la clave, no pueda entrar sin el token. 

## Dinámica:

- Fase de Ataque: Desde tu máquina atacante, usar Hydra para realizar un ataque de diccionario contra el servicio SSH del Ubuntu.
- Fase de Hardening (MFA): En el Ubuntu Server, instalar el módulo PAM de Google Authenticator.
- Ejecutar google-authenticator y configurar la App en el celular.
- Configuración de SSH: Modificar /etc/pam.d/sshd y /etc/ssh/sshd_config para exigir tanto la contraseña como el token (MFA).
- Prueba Final: Volver a intentar el ataque con Hydra. Aunque Hydra encuentre la contraseña, la conexión fallará porque no puede saltarse el segundo factor.

## Verificación de las herramientas

Verificar si el servicio SSH esta corriendo desde la máquina víctima

<img width="802" height="292" alt="Verificación del ssh" src="https://github.com/user-attachments/assets/25d14f69-1a00-43dd-974f-18a6a3e372e8" />

Segunda verificación desde el atacante

<img width="1277" height="398" alt="Verificación del ssh 2" src="https://github.com/user-attachments/assets/a003fd6a-d395-480b-94f7-5baace3368e7" />

Verificación de hydra

<img width="1885" height="163" alt="Verificación hydra" src="https://github.com/user-attachments/assets/38d48ec8-4418-440f-8254-a1f8fa24a645" />

Verificación si se tiene el diccionario rockyou.txt

<img width="528" height="167" alt="Verificación del rockyou" src="https://github.com/user-attachments/assets/25c1f67a-520e-4c53-9854-248c95b7f0a7" />

## Pasos del laboratorio

### Ataque de diccionario a la máquina vulnerable

Desde la máquina atacante se realizó el ataque contra el SSH.

<img width="1867" height="406" alt="Ataque de diccionario" src="https://github.com/user-attachments/assets/43e90a0a-cba6-496f-8fd7-113a5026ee75" />

El comando realiza:

múltiples intentos de autenticación usando un usuario específico y probando cada contraseña del diccionario.

Si la contraseña es débil y se encuentra dentro del diccionario, Hydra mostrará el acceso exitoso.

Al tener tanto el usuario como la contraseña, ya se puede realizar la conexión remota mediante SSH

<img width="1585" height="938" alt="Captura de pantalla 2026-05-11 222434" src="https://github.com/user-attachments/assets/39bb4f76-0d3e-4b97-8ad4-0890c1e54941" />

### Instalación del Google Authenticator PAM

Para securizar nuestro conexión SSH, en la máquina víctima se instala el módulo MFA.

<img width="805" height="488" alt="Descargar paquete de autenticación de google" src="https://github.com/user-attachments/assets/3be8ed20-1d73-4901-b606-027d87e16656" />

Este módulo permite integrar autenticación multifactor mediante códigos temporales TOTP.

### Configuración del Google Authenticator

Ejecutamos el google-authenticator y posteriormente le damos que si (yes) a todas las opciones que vayan apareciendo.

<img width="927" height="80" alt="Captura de pantalla 2026-05-11 233206" src="https://github.com/user-attachments/assets/3641ae4f-1e7c-43e7-b3f5-777f779822df" />

Esto nos genera un código QR, crea claves secretas, y habilita tokens temporales para MFA. Al realizar la vinculación (mediante un escaneo al QR desde el teléfono), la aplicación comienza a generar códigos temporales de autenticación (TOTP) que cambian automáticamente cada pocos segundos. Estos códigos funcionan como un segundo factor de seguridad adicional al momento de iniciar sesión mediante SSH.

De esta manera, aunque un atacante consiga la contraseña correcta del usuario, no podrá acceder al servidor sin disponer también del código temporal generado en el dispositivo móvil autorizado.

<img width="1188" height="967" alt="Captura de pantalla 2026-05-11 233151" src="https://github.com/user-attachments/assets/066be74c-41f2-446c-b117-45dd32b8ee66" />

PD: Lo tuve que realizar conectandome desde WSL2, porque el QR se veía incompleto desde la pantalla de la máquina virtual :^

### Configurar PAM para SSH

Para que el servidor realmente exija el código, debemos editar dos archivos críticos:

Archivo PAM ( sudo nano /etc/pam.d/sshd): Aquí le decimos al sistema operativo que incluya el módulo de autenticación de Google.

<img width="1176" height="965" alt="Captura de pantalla 2026-05-11 233831" src="https://github.com/user-attachments/assets/cd27aba7-80a2-4eba-a804-c29a982fc44f" />

Nos vamos al final del archivo y colocamos la siguiente línea: "auth required pam_google_authenticator.so", con esto obligamos al sistema a solicitar un segundo factor de autenticación durante el inicio de sesión SSH.

### Configurar SSH para MFA

Archivo SSH (sudo nano /etc/ssh/sshd_config): Aquí configuramos que el servicio SSH permita y exija métodos de autenticación interactivos. Dentro del archivo encontraremos una línea que dice: "ChallengeResponseAuthentication no", cambiamos el no por el "yes".

<img width="1182" height="960" alt="Captura de pantalla 2026-05-12 000632" src="https://github.com/user-attachments/assets/2886bb2e-e7f5-414c-8f81-dadb5053dff3" />

<img width="996" height="241" alt="Captura de pantalla 2026-05-12 000743" src="https://github.com/user-attachments/assets/5005e72e-8cbe-471d-9c34-00cb4fb92f7d" />

Con esto permitimos que SSH pueda solicitar un segundo desafío de autenticación además de la contraseña tradicional.

### Reinicio del servicio

Colocamos el comando: "sudo systemctl restart ssh". Es necesario reiniciar para poder aplicar los cambios realizados en la configuración.

<img width="671" height="107" alt="Captura de pantalla 2026-05-12 000848" src="https://github.com/user-attachments/assets/0a8eef3f-8269-4a75-83c3-7bb7b0d92345" />

### Verificar acceso con MFA

Ahora si trato de conectarme al servidor ssh de mi máquina víctima me pedirá el código de verificación.

<img width="714" height="62" alt="Captura de pantalla 2026-05-12 001726" src="https://github.com/user-attachments/assets/e526546e-8723-4562-a4d3-0d6ed0f1dca0" />

Aunque tenga la contraseña, si no tengo el código para verificarme, no podre conectarme mediante SSH.

# Conclusión

Aprendizaje
   
Se demostró la vulnerabilidad de los sistemas basados solo en contraseñas mediante ataques de fuerza bruta con Hydra. El aprendizaje clave fue la implementación de Autenticación Multifactor (MFA) usando el estándar TOTP (contraseñas temporales), añadiendo una capa de seguridad física (el teléfono) al proceso de acceso.

Prevención de Ataques
Este control es la defensa definitiva contra:
- Ataques de Diccionario y Fuerza Bruta: Aunque el atacante logre descifrar la contraseña, el acceso es denegado al no poseer el token dinámico.
- Robo de Credenciales: Mitiga el impacto de contraseñas filtradas, asegurando que la posesión de la clave no sea suficiente para comprometer el sistema.
