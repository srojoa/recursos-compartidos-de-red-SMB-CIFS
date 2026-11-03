# Configuracion/Instalación Windows 11


<img width="841" height="629" alt="image" src="https://github.com/user-attachments/assets/f5133e1b-82fc-4abb-a532-06ead32f7b5e" />


<img width="882" height="629" alt="image" src="https://github.com/user-attachments/assets/fc083b39-556e-4526-b443-9baa09dfd9a5" />


<img width="1261" height="629" alt="image" src="https://github.com/user-attachments/assets/1a6732f2-3717-4191-9772-1772197b3585" />


<img width="840" height="629" alt="image" src="https://github.com/user-attachments/assets/a064d9aa-d567-411a-b4ed-4fda7c244362" />


<img width="840" height="629" alt="image" src="https://github.com/user-attachments/assets/17fc8672-696c-442a-ba5d-50c5620a2919" />


<img width="840" height="629" alt="image" src="https://github.com/user-attachments/assets/3a54ac7d-95ce-4120-9a87-7dca6a947085" />


<img width="840" height="629" alt="image" src="https://github.com/user-attachments/assets/e86e2e20-888b-43db-a1da-4f684a4a72b5" />



**En este punto:**

<img width="1188" height="851" alt="image" src="https://github.com/user-attachments/assets/f4b299cf-349a-40f1-bcb4-d9f26a4b477a" />

Presiona **Shift + F10** para abrir el Command Prompt.
Escribe **OOBE\BYPASSNRO** y presiona **ENTER**

<img width="625" height="332" alt="image" src="https://github.com/user-attachments/assets/a16d1c6c-6beb-4746-813d-0e9a6945fe05" />


<img width="1060" height="777" alt="image" src="https://github.com/user-attachments/assets/e2e27e81-4ae4-4551-a03c-b8600be2745e" />

Contrroladora Ethernet 
                      |
                      --> Click Derecho 
                                      |
                                      --> Actualizar Controlador
                                                               |
                                                               --> Buscar Manualmente 
                                                                                    |
                                                                                    --> Examinar

<img width="261" height="255" alt="image" src="https://github.com/user-attachments/assets/ebc3de0a-df7f-438d-baa3-05d1ca6a1ca1" />

<img width="261" height="255" alt="image" src="https://github.com/user-attachments/assets/c626aebe-fdf3-44ad-bde5-2cef9aaf7d40" />

<img width="261" height="255" alt="image" src="https://github.com/user-attachments/assets/06bd86d9-482a-40ea-ad04-502eff7f4b9e" />


**Paso 1: Configurar IP y Nombre de equipo en Windows 11**

* **A. Cambiar el Nombre del equipo:**
  
  Abre Configuración (Settings) -> Sistema (System) -> Acerca de (About).
  
  Haz clic en Cambiar el nombre de este equipo (Rename this PC).
  
  Escribe client01w y reinicia el equipo cuando te lo pida.
  
 * **B. Configurar la IP estática:**
  
  Ve a Configuración -> Red e Internet (Network & Internet) -> Ethernet.
  
  Haz clic en Editar (Edit) al lado de "Asignación de IP" (IP assignment).
  
  Cambia a Manual y activa IPv4.
  
  Rellena los datos:
  
  Dirección IP: 172.26.1.11
  
  Máscara de subred: 255.255.0.0 (o /16)
  
  Puerta de enlace: 172.26.0.254
  
  DNS preferido: 172.26.0.254
  
  Haz clic en Guardar.


📝 **Paso 2: Configurar el archivo hosts de Windows**
El archivo hosts permite que Windows resuelva el nombre del servidor (samuel01g) directamente a su IP sin usar un servidor DNS.

Haz clic en el menú de Inicio, escribe Bloc de notas (Notepad), haz clic derecho y selecciona Ejecutar como administrador.

En el Bloc de notas, ve a Archivo -> Abrir.

Navega hasta esta ruta: C:\Windows\System32\drivers\etc

Abajo a la derecha, cambia el filtro de "Documentos de texto (.txt)" a **"Todos los archivos (.*)"**.

Selecciona el archivo hosts y ábrelo.

Baja hasta el final del archivo y añade estas líneas (la IP de tu servidor y tu cliente Linux):

>``` 
> 172.26.1.31    samuel01g
> 172.26.1.32    client01g
Guarda el archivo (Ctrl + S) y ciérralo.

🔗 **Paso 3: Conectar a los recursos compartidos de Samba**
El software ya viene preinstalado en Windows (el cliente SMB). Vamos a probar las conexiones:

1. Probar el recurso público (Invitado / Solo lectura):

Pulsa Win + R para abrir "Ejecutar".

Escribe \\\172.26.1.31\public01 o \\samuel01g\public01 y pulsa Enter.

Debería abrirse la carpeta sin pedir contraseña. Intenta crear un archivo de texto ahí; debería darte un error de "Permiso denegado" (porque es de solo lectura).

2. Probar el recurso castillo (Soldados / Lectura-Escritura):

Pulsa Win + R y escribe \\\172.26.1.31\castillo01.

Te pedirá credenciales. Introduce:

Usuario: soldado1

Contraseña: (la que creaste con smbpasswd).

(Si te da error, en el campo usuario escribe CURSO2627\soldado1 para indicar el dominio/grupo de trabajo).

Debería abrirse. Intenta crear una carpeta dentro.

3. Demostrar la restricción:

Si intentas entrar a \\\172.26.1.31\castillo01 usando el usuario pirata1, Windows te denegará el acceso.

💡 Truco para Windows: Si Windows se queda con una contraseña guardada en caché y no te deja meter otra (por ejemplo, si ya entraste como soldado y ahora quieres entrar como pirata), abre el Panel de Control -> Administrador de credenciales -> Credenciales de Windows, y borra las que tengan que ver con 172.26.1.31 o samuel01g.

<img width="629" height="400" alt="Screenshot From 2026-11-03 02-38-05" src="https://github.com/user-attachments/assets/bc0288ad-a3cc-4348-8798-a621e7b1fa20" />
<img width="787" height="481" alt="Screenshot From 2026-11-03 02-39-18" src="https://github.com/user-attachments/assets/14516da7-ccc8-4f5f-b396-c56fcf8bef29" />







