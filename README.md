# Metasploitable 2 Linux

## Configuración

### Paso 1 : Descarga

- Para esta auditoría de pentesting vamos a usar esta instancia de Metasploit y Una instancia de Windows server 2003:

  ![image](https://github.com/user-attachments/assets/145157f9-b7db-4de4-8573-9a0180b1c087)

  --------------------------------------------------------------------------------------------------------------------

  ![image](https://github.com/user-attachments/assets/50c624b5-b072-4a3a-b26d-cbde84bb5793)


  https://information.rapid7.com/metasploitable-download.html

  https://sourceforge.net/projects/metasploitable/
   
  P.D.: Con la instancia que uso, no tenemos las contraseñas para acceder al servidor.

### Paso 2 : Producto de virtualización

- Descomprimir el archivo

- Usar VMWare o VirtualBox para añadir la VM

### Paso 3 : Configuración virtual

- Poner la máquina en la misma subred que tu máquina de ataque

La IP de la VM Metasploitable 2 es 192.168.10.100/24

### Paso 4 : Inicio

- Iniciar ambas VM

### Advertencia

Nunca exponer la VM Metasploitable a internet, siempre en local

Recomiendo usar una VM Kali para realizar el ataque, puedes encontrar algunas VM preconfiguradas aquí:

[Kali offensive security](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/#1572305786534-030ce714-cc3b)

## Empecemos a hackear

### Amenaza n°1 : Netcat bindshell – Metasploitable root shell – Puerto 1524
#### Descripción
Una shell de superusuario está disponible en el puerto 1524.

#### Referencias
N/A

#### Operación e impacto
![image](https://user-images.githubusercontent.com/44178372/114245936-b3c0d980-9991-11eb-9b8a-a7717ea3fdb4.png)

Iniciamos sesión como root.

#### Corrección

Deshabilitar este puerto, así como la shell si no es necesaria.

### Amenaza n°2 : Backdoor FTP – vsftpd 2.3.4 – Puerto 21
#### Descripción
Esta vulnerabilidad está relacionada con una puerta trasera que se agregó al archivo de descarga de VSFTPD. Esta puerta trasera se introdujo en el archivo vsftpd-2.3.4.tar.gz entre el 30 de junio de 2011 y el 1 de julio de 2011.
#### Referencias
https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor/

#### Operación e impacto

Usamos el exploit presente en Metasploit e iniciamos sesión como root.

![image](https://user-images.githubusercontent.com/44178372/114246273-7f015200-9992-11eb-972b-60d1e7b5b02c.png)

#### Corrección

Actualizar la versión del FTP.

### Amenaza n°3 : VNC – Puerto 5900
#### Descripción
La contraseña para conectarse al puerto VNC es débil.
#### Referencias
https://www.rapid7.com/db/modules/auxiliary/scanner/vnc/vnc_login/
#### Operación e impacto
![image](https://user-images.githubusercontent.com/44178372/114246477-fcc55d80-9992-11eb-9a8c-1ba9a3d1c1bb.png)

![image](https://user-images.githubusercontent.com/44178372/114246480-fdf68a80-9992-11eb-9a8e-ad5a8fbb49b8.png)

Usamos una puerta trasera que recupera la contraseña. En este caso, es idéntica a la contraseña predeterminada del servicio VNC viewer.
Luego iniciamos sesión e ingresamos la contraseña.

![image](https://user-images.githubusercontent.com/44178372/114246495-08b11f80-9993-11eb-8141-3c774ccfad9d.png)

Obtenemos una interfaz gráfica.

![image](https://user-images.githubusercontent.com/44178372/114246503-11a1f100-9993-11eb-83e6-7466f1a298eb.png)

Iniciamos sesión como root.
#### Corrección
Se debe cambiar la contraseña por una contraseña fuerte.

### Amenaza n°4 : Servicios « R » – Puerto 512/513/514
#### Descripción
Los puertos TCP 512, 513 y 514 son conocidos como servicios "r" que pueden permitir a un atacante ingresar al sistema si están configurados incorrectamente.
Los servicios RSH Remote Shell (rsh, rexec y rlogin) están activos.
#### Referencias
N/A
#### Operación e impacto
![image](https://user-images.githubusercontent.com/44178372/114246554-36966400-9993-11eb-9172-d010c5176aa9.png)

Usamos la herramienta rsh-client para poder utilizar el comando rlogin y por lo tanto conectarnos como root directamente en la máquina, sin contraseña.
#### Corrección
Deshabilitar o usar un firewall para asegurar este servicio que generalmente funciona en 512/513/514/tcp.

### Amenaza n°5 : NFS – Puerto 2049
#### Descripción
No se necesita autenticación para realizar acciones sensibles en el puerto 2049.
#### Referencias
N/A
#### Operación e impacto
![image](https://user-images.githubusercontent.com/44178372/114246610-53329c00-9993-11eb-87ec-93df83c6b6ab.png)

Montamos una carpeta local en el servidor NFS de la máquina objetivo.

![image](https://user-images.githubusercontent.com/44178372/114246615-56c62300-9993-11eb-8b2d-cd0ee85988cc.png)

Agregamos nuestra clave ssh en las claves autorizadas.

![image](https://user-images.githubusercontent.com/44178372/114246627-5e85c780-9993-11eb-9566-4232c7725d89.png)

Iniciamos sesión como root.
#### Corrección
Agregar autenticación antes de enviar archivos/carpetas.

### Amenaza n°6 : Backdoor IRC – UnrealIRCd – Puerto 6667
#### Descripción
Este problema está relacionado con el puerto 6667 que ejecuta el daemon UnrealRCD IRC, una versión que contiene una puerta trasera.
#### Referencias
https://www.rapid7.com/db/modules/exploit/unix/irc/unreal_ircd_3281_backdoor/

https://www.cvedetails.com/vulnerability-list/vendor_id-10938/Unrealircd.html
#### Operación e impacto
![image](https://user-images.githubusercontent.com/44178372/114246716-9725a100-9993-11eb-926c-c91a9371f919.png)

Iniciamos sesión como root.
#### Corrección
Actualizar la versión de IRC.

### Amenaza n°7 : Netbios-ssn – Samba smbd 3.X – 4.X – Puerto 139/445
#### Descripción
Esta vulnerabilidad está relacionada con una vulnerabilidad de ejecución de comandos en las versiones de Samba 3.0.20 a 3.0.25rc3 cuando se usa la opción de configuración "username map script" diferente a la predeterminada.
#### Referencias
https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script/
![image](https://user-images.githubusercontent.com/44178372/114246828-eb308580-9993-11eb-865b-76636a2f65b0.png)

#### Operación e impacto
Usamos el exploit presente en Metasploit e iniciamos sesión como root.
#### Corrección
Actualizar la versión de Samba.

### Amenaza n°8 : Java-rmi – Puerto 1099
#### Descripción
Esta vulnerabilidad está relacionada con la configuración predeterminada de los servicios de registro RMI y activación RMI, que permiten que las clases se carguen desde cualquier URL remota (HTTP).
#### Referencias
https://www.rapid7.com/db/modules/exploit/multi/misc/java_rmi_server/
#### Operación e impacto
Usamos el exploit presente en Metasploit.

![image](https://user-images.githubusercontent.com/44178372/114246878-13b87f80-9994-11eb-88a7-438829bff7b9.png)

Obtenemos un meterpreter que nos permite obtener una shell, lo que nos da acceso de superusuario.

![image](https://user-images.githubusercontent.com/44178372/114246891-1d41e780-9994-11eb-907a-c6873cf40542.png)

Iniciamos sesión como root.

#### Corrección

Actualizar la versión de Java para parchear la puerta trasera.

## Captura de pantalla Nessus y Escaneo.
![image](https://github.com/user-attachments/assets/78561939-10de-4d31-a341-233cf8db2c8c)

