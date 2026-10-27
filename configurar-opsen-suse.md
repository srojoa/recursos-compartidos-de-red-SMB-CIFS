# Parte 1 Configurar MV GNU/Linux OpenSUSE

# Configurar MV GNU/Linux OpenSUSE

# Antes de empezar

Crear la máquina ProxMox con:
* Crear un **disco de 20 GB**.
* Tarjeta de red en `modo puente`:

Dentro del **Nodo** tenemos que tener algo tal que así:

<img width="197" height="68" alt="image" src="https://github.com/user-attachments/assets/cf7f5cef-507f-42e2-bff2-a0655cfda2d3" />
<img width="1304" height="77" alt="image" src="https://github.com/user-attachments/assets/ee059c5b-d787-4cd2-8612-8c539e6a08c2" />




> Para comprobar la ISO de instalación usamos el siguiente comando: `sha256sum -c opensuse-version.sha256`.

# Durante la instalación del SO

* Redordar: Hacer la primera partición FAT32 de 512 MB para Boot EFI(/boot/efi).

<img width="1796" height="1049" alt="image" src="https://github.com/user-attachments/assets/84c678c8-c697-41ad-bdb7-7b74251e8c69" />


* Crear usuario identificado como `nombre-del-alumno`.
* Seleccionar entorno gráfico ligero (XFCE preferiblemente).
    * Escritorio -> XFCE.
 
    <img width="1704" height="712" alt="image" src="https://github.com/user-attachments/assets/d8d17409-7332-4c55-ad39-d485f02a0aa1" />


* Recordatorio.
    * Instalar SSH:
    ```bash
    sudo zypper install -y openssh-server`
    ```
    * Habilitar servicio SSH:
   ```bash
   sudo systemctl enable --now sshd
   ```

    Verificar el estado:
   ```bash
   sudo systemctl status sshd
   ```

   Abrir el puerto en el cortafuegos (Firewall) de openSUSE:
   ```bash
   sudo firewall-cmd --permanent --add-service=ssh
   sudo firewall-cmd --reload
   ```
    
* Configurar lo siguiente durante la instalación:

> ![opensuse-instalacion-configuracion.png](./images/opensuse-instalacion-configuracion.png)

---
# Con el sistema operativo ya instalado
Cambiar el nombre del equipo (Hostname)
```bash
sudo hostnamectl set-hostname nombre-del-alumno
```

> * Donde aparezca XX debemos poner el código asignado al alumno. Esto es su número de PC.

**Configurar la red y el nombre del equipo mediante COMANDOS** Una vez instalado el sistema operativo, podemos hacer cambios en la configuración de red usando nano /etc/hosts. Vamos a `Inicio -> 
* Seleccionamos el interfaz de red por defecto y editamos.
* Marcamos IP fija.
* IP: `172.26.XX.31` (Donde XX corresponde al nº de cada puesto).
    * Máscara de red: `255.255.0.0`
```bash
sudo nano /etc/hosts
```

>```
>  GNU nano 8.6                                  /etc/hosts                                             
>#
># hosts         This file describes a number of hostname-to-address
>#               mappings for the TCP/IP subsystem.  It is mostly
>#               used at boot time, when no name servers are running.
>#               On small systems, this file can be used instead of a
>#               "named" name server.
># Syntax:
>#
># IP-Address  Full-Qualified-Hostname  Short-Hostname
>#
>
>127.0.0.1       localhost localhost.localdomain
>::1             localhost localhost.localdomain ipv6-localhost ipv6-loopback
>
># special IPv6 addresses
>fe00::0         ipv6-localnet
>
>ff00::0         ipv6-mcastprefix
>ff02::1         ipv6-allnodes
>ff02::2         ipv6-allrouters
>ff02::3         ipv6-allhosts
>
>172.26.01.31    nombre-del-alumno
```


# Supongamos que tu conexión se llama "Wired connection 1" (o similar). Ejecuta estos comandos:
```bash
nmcli connection show
```


1. Asignamos la IP y la máscara:
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.addresses 172.26.XX.31/16
```
2. Poner el método en manual (IP fija):
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual
```

3. Aplicar los cambios reiniciando la conexión:
```bash
sudo nmcli connection up "Wired connection 1"`
```

* Siguiente

Vamos a `Nombre de Host/DNS` y hacemos lo siguiente:
* Desmarcamos `Modificar nombre mediante DHCP`
* Marcamos `Asignar nombre de host a la IP bucle local`
* Nombre de equipo: `primer-apellido-del-alumnoXXg`.
    * Por ejemplo vargas30g
* Nombre de dominio: `curso2627`
* Servidor DNS: `9.9.9.9`.
* Vamos a `Encaminamiento`, y ponemos Gateway o pasarela IPv4: `172.AA.0.1`. Esto es la puerta de enlace o encaminamiento.
* Ir dispositivo y elegir el interfaz de red.


> INFORMACIÓN sobre los **ficheros de configuración de red** gestionados por Wicked.
>
> El fichero de configuración de red es `/etc/sysconfig/network/ifcfg-eth0` para la tarjeta `eth0`.
> Ejemplo de configuración del modo automático o dinámico:
> ```
> BOOTPROTO='dhcp'
> STARTMODE='auto'
> DHCLIENT_SET_DEFAULT_ROUTE='yes'
> ```
>
> Ejemplo de configuración del modo manual o estático:
> ```
> BOOTPROTO='static'
> STARTMODE='auto'
> IPADDR='192.168.16.11/24'
> ```
>
> Fichero de configuración de la puerta de enlace `/etc/sysconfig/network/ifroute-eth0` para la tarjeta `eth0`. Ejemplo: `default 172.19.0.1 - eth0`
>
> Fichero de configuración del servidor DNS `/etc/sysconfig/network/config`. Parámetro
`NETCONFIG_DNS_STATIC_SERVERS="9.9.9.9"`

---
# Comprobaciones finales

**Comprobar la configuración de RED**. Cuando la red no vaya bien, debemos primero hacer estas comprobaciones para averiguar dónde tenemos el problema.

```
ip a                     # Información de red
ip route                 # Información de la puerta de enlace
ifstatus NOMBREINTERFAZ  # Configuración del interfaz de red
ping 9.9.9.9             # Verifica la conectividad con Internet
traceroute 9.9.9.9       # Comprobar enrutamiento   
host www.nba.com         # Comprueba DNS
```

> * Si tenemos varias máquinas usaremos las IP 172.AA.XX.32, 172.AA.XX.33, etc.
> * Si tenemos varias máquinas las llamaremos vargas30g2, vargas30g3, vargas30g4, etc.

**Usuario**. Comprobar que se ha creado el usuario correctamente con los siguientes comandos:
* `id nombre-alumno`, muestra información del usuario
* `su -l nombre-alumno`, abrimos una sesión con el usuario.

**Nombre de EQUIPO**: Comprobar nombre del equipo.
* Comprobar hostname. Ejemplo:
```
david@vargas42g:~> cat /etc/hostname
vargas42g.curso1819
```
* Comprobar host. Ejemplo:

```
david@vargas42g:~> cat /etc/hosts

127.0.0.1	localhost
127.0.0.2 vargas42g.curso1819   vargas42g

# special IPv6 addresses
::1             localhost ipv6-localhost ipv6-loopback
fe00::0         ipv6-localnet
ff00::0         ipv6-mcastprefix
ff02::1         ipv6-allnodes
ff02::2         ipv6-allrouters
ff02::3         ipv6-allhosts
```

* Comprobar con los siguientes comandos:

```
hostname -f      # Muestra nombre-maquina.nombre-dominio
hostname -a      # Muestra nombre-maquina
hostname -d      # Muestra nombre-dominio
```

---
# ANEXO

## Cambiar el idioma/country desde comandos

https://www.suse.com/documentation/opensuse110/opensuse110_reference/data/sec_suse_l10n.html

## End of life

https://news.opensuse.org/2022/01/13/opensuse-reaches-eol/

Users can upgrade from 15.2 to 15.3 by downloading the iso image
or following the instructions on how to upgrade
found on https://en.opensuse.org/SDB:System_upgrade.
