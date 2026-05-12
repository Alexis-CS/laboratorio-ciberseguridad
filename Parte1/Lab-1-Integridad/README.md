# Laboratorio 1 — Firma Digital y Verificación de Integridad

## Tecnologías y herramientas utilizadas:
- Ubuntu Server 18.04
- OpenSSL
- sha256sum
- Terminal de Linux
<img width="713" height="611" alt="Maquina vulnerable" src="https://github.com/user-attachments/assets/9ea58a54-3ade-4451-88b1-4445364de334" />

## Descripción general
- Actividad: Configuración de firma digital y verificación de integridad.
- Comandos clave: openssl, sha256sum, gpg.
- Tarea: Los alumnos deben generar un par de llaves (Pública/Privada), firmar un archivo de configuración crítico y verificar que si se cambia un solo bit del archivo, el hash de integridad falla.
- Enfoque organizacional: Simular la firma de un manifiesto de software antes de subirlo a producción.

## Dinámica:
- Generación de Hash: Crea un archivo config_bancaria.txt. Usa sha256sum para generar su huella digital.
- Simulación de Ataque: Un "atacante" modifica un solo carácter (ej: cambia un 0 por un 1 en un monto).
- Verificación: El alumno vuelve a correr el hash y nota que la "huella" es totalmente distinta.
### Firma Digital (OpenSSL):
- Generar llave privada:
- Generar llave pública
- Firmar archivo
  
<img width="810" height="73" alt="Creación del archivo" src="https://github.com/user-attachments/assets/65b56bc7-a469-4bce-b921-80caa6ef7ba9" />

Este archivo representa una configuración crítica que podría pertenecer a: un sistema bancario, una aplicación empresarial, o un servidor de producción.

## Generar el hash SHA-256 del archivo

<img width="805" height="80" alt="sha256sum al archivo" src="https://github.com/user-attachments/assets/a9b8920a-ab2b-424d-9fea-52d6cc650dfa" />

Explicación

El comando sha256sum genera un hash único del archivo.

El hash permite: 
- Identificar el contenido exacto.
- Verificar la integridad.
- Detectar cualquier modificación.
  
Si el archivo cambia aunque sea un solo carácter, el hash será completamente diferente.

## Simular una modificación maliciosa
Ahora se modifica el archivo para simular un ataque o alteración no autorizada.

<img width="801" height="602" alt="Cambio del contenido" src="https://github.com/user-attachments/assets/ad592199-8bd8-44c9-a50b-58c66af75d80" />

El contenido original del archivo es "Monto: 1000", y realizamos un cambio minimo que es cambiar el número 1000 por 5000.

<img width="808" height="86" alt="sha256sum aplicada al archivo denuevo" src="https://github.com/user-attachments/assets/87fc149d-d42f-493e-91c6-ccde6e3ea310" />

Observamos como cambio por completo el hash del archivo anterior de forma radical (pueden comparar por las imágenes). Lo anterior permite demostrar el efecto avalancha del hashing: un pequeño cambio produce un hash totalmente distinto.

## Generaración de la llave privada
Se genera una llave privada de 2048 bits utilizando OpenSSL.

<img width="807" height="171" alt="Generación de la llave privada" src="https://github.com/user-attachments/assets/98d02920-5417-44fe-a195-58a86d88df4d" />

Verificamos la creación de la llave privada.

<img width="792" height="606" alt="Verificación de la llave privada" src="https://github.com/user-attachments/assets/951de115-f5ce-48c6-b6ec-7dc3997dfe34" />

Explicación:

La llave privada sirve para firmar digitalmente, por ende, debe mantenerse protegida y nunca debe compartirse públicamente.

## Generación de la llave pública

A partir de la llave privada se crea la llave pública.

<img width="807" height="107" alt="Creación llave publica" src="https://github.com/user-attachments/assets/48851f4c-ed52-43b5-bd4b-140117250e3c" />

Verificamos la creación de la llave pública

<img width="802" height="598" alt="verificación de la llave publica" src="https://github.com/user-attachments/assets/fc186a0c-54d0-47e2-9b08-056132dbcb54" />

Explicación

La llave pública si puede compartirse, permitiendo verificar firmas digitales y confirmar autenticidad del archivo firmado.

## Firmar digitalmente el archivo
Se genera una firma digital del archivo utilizando SHA-256 y la llave privada.

<img width="807" height="96" alt="Creación de la firma" src="https://github.com/user-attachments/assets/811e1d75-fb60-4c35-b3f7-38153fdcb3a5" />

Explicación:

Este proceso permite calcular el hash del archivo, cifra el hash con la llave privada, y genera una firma digital.

La firma garantiza:

- Autenticidad.
- Integridad.
- No repudio.

Con el siguiente comando realizamos la validación de la firma digital usando la llave pública.

<img width="810" height="120" alt="Validación de la firma digital" src="https://github.com/user-attachments/assets/87ae40a3-9db1-4501-bb3f-0cc1ca1db12d" />

# Conclusiones

Aprendizaje

Se dominó el uso de hashing (SHA-256) y criptografía asimétrica (RSA) con OpenSSL. El aprendizaje clave es que la seguridad no es solo ocultar datos, sino garantizar que no han sido alterados (Integridad) y que el emisor es quien dice ser (Autenticidad/No repudio).

Prevención de Ataques
Estas técnicas blindan el sistema contra:
- Man-in-the-Middle (MitM): Detecta cualquier cambio en los datos durante el envío gracias al "efecto avalancha" del hash.
- Suplantación de Identidad: El uso de llaves privadas asegura que la firma solo pudo ser generada por el dueño legítimo.
