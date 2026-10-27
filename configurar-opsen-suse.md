# Parte 1 Configurar MV GNU/Linux OpenSUSE

# Configurar MV GNU/Linux OpenSUSE
**MAPA VISUAL:**
<img width="1237" height="187" alt="image" src="https://github.com/user-attachments/assets/2e912439-8b8e-4182-b893-b6b6c2a50eef" />

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
     ```bash
    sudo zypper refresh && sudo zypper update
    ```
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
>172.26.1.31     nombre-del-alumno


* Supongamos que tu conexión se llama "Wired connection 1" (o similar). Ejecuta estos comandos:
```bash
nmcli connection show
```
<img width="727" height="70" alt="image" src="https://github.com/user-attachments/assets/e3a6c93f-4af8-4d80-98ff-b78f282ed43b" />

1. Asignamos la **IP** y la **máscara**:
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.addresses 172.26.XX.31/16
```
2. Poner el método en manual (**IP fija**):
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual
```

3. Aplicar los cambios reiniciando la conexión:
```bash
sudo nmcli connection up "Wired connection 1"
```

* Siguiente

Vamos a `Nombre de Host/DNS` y hacemos lo siguiente:
* Desmarcamos `Modificar nombre mediante DHCP`
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dhcp-send-hostname no
```
* Marcamos `Asignar nombre de host a la IP bucle local`
* Nombre de equipo: `primer-apellido-del-alumnoXXg`.
    * Por ejemplo **samuel01g**
* Nombre de dominio: `**curso2627**`
```bash
sudo hostnamectl set-hostname samuel01g.curso2627
```
* Servidor DNS: `9.9.9.9`.
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dns "9.9.9.9"
# Asignacion de nombre de host a la IP bucle local
echo "127.0.0.1 samuel01g.curso2627 samuel01g" | sudo tee -a /etc/hosts
```
* Vamos a Encaminamiento, y ponemos Gateway o pasarela IPv4: "172.26.0.254". Esto es la puerta de enlace o encaminamiento:
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.gateway "172.26.0.254"
```

* Interfaz de Red y Aplicación de Cambios:
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual
sudo nmcli connection down "Wired connection 1" && sudo nmcli connection up "Wired connection 1"
```

✅ Verificación final:
```bash
hostnamectl status
nmcli connection show "Wired connection 1" | grep ipv4
``` 
* En el nodo **root the Proxmox** abrimos la shell:
<img width="1255" height="302" alt="image" src="https://github.com/user-attachments/assets/f3be3b1f-b072-42b2-ad2b-d2ba1b18e0bb" />

>root@proxmox:~# cat /etc/network/interfaces
>```auto lo
>iface lo inet loopback
>
>iface nic0 inet manual
>
>auto vmbr0
>iface vmbr0 inet static
>        address 192.168.122.104/24
>        gateway 192.168.122.1
>        bridge-ports nic0
>        bridge-stp off
>        bridge-fd 0
>        # Añadir la IP secundaria para la red de la VM
>        up ip addr add 172.26.0.254/16 dev vmbr0
>        down ip addr del 172.26.0.254/16 dev vmbr0
>        # Forzar el origen correcto para la red de la VM
>        up ip route add 172.26.0.0/16 dev vmbr0 src 172.26.0.254
>        # Activar el reenvío de paquetes (IP Forwarding)
>        up sysctl -w net.ipv4.ip_forward=1
>        # Aceptar el tráfico de una red a otra
>        up iptables -P FORWARD ACCEPT
>        # Regla de redirección de puertos (SSH por el puerto 2222)
>        up iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 2222 -j DNAT --to-destination 172.26.1.31:22
>        down iptables -t nat -D PREROUTING -i vmbr0 -p tcp --dport 2222 -j DNAT --to-destination 172.26.1.31:22
```

```bash
ifreload -a
``` 

Desde nuestro Host de tu PC FÍSICO:
```bash
ssh nombre-del-alumno@192.168.122.104 -p 2222
``` 

>```The authenticity of host '[192.168.122.104]:2222 ([192.168.122.104]:2222)' can't be established.
>ED25519 key fingerprint is: SHA256:0nrlIc9YjdexpF75LZK9Bj5Q+cw+vXAxmZ4/eCXPfcU
>This host key is known by the following other names/addresses:
>    ~/.ssh/known_hosts:4: [hashed name]
>Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
>Warning: Permanently added '[192.168.122.104]:2222' (ED25519) to the list of known hosts.
>(samuel@192.168.122.104) Password: 
>Have a lot of fun...
>samuel@suse:~> $



---
# Comprobaciones finales

**Comprobar la configuración de RED**. Cuando la red no vaya bien, debemos primero hacer estas comprobaciones para averiguar dónde tenemos el problema.

```
🐧 FASE 1: Comprobaciones DENTRO de la Máquina Virtual (OpenSUSE)
ip a                            # Información de red
ip route                        # Información de la puerta de enlace
sudo systemctl status sshd      # El servicio SSH está vivo?
sudo systemctl status firewalld # El cortafuegos interno está bloqueando?

🛡️ FASE 2: Comprobaciones (Proxmox)
ip a show vmbr0                 # El puente (Bridge) tiene las IPs correctas?
ip route get 172.26.XX.31       # El enrutamiento está bien dirigido?
sysctl net.ipv4.ip_forward      # El sistema actúa como router? net.ipv4.ip_forward = 1
```
   >net.ipv4.ip_forward = 1
```
🔥FASE 3: El "Muro de los Lamentos" (Cortafuegos de Proxmox)
qm list
qm config 101 | grep firewall  # El firewall de la VM está bloqueando?

```

   >boot: order=scsi0;ide2;net0
   >net0: virtio=BC:24:11:a1:eb:ca,bridge=vmbr0,firewall=0

```
🌐 FASE 4: Comprobaciones de Conectividad (El "Ping")
1. Desde la VM a Proxmox:

ping -c 4 172.26.0.254

2. Desde Proxmox a la VM:

ping -c 4 172.26.1.31

3. El "Espía" definitivo:
tcpdump -i any -n icmp # Ejecuta Desde Proxmox
ping -c 4 172.26.1.31  # Ejecuta Desde OpenSUSE
```



