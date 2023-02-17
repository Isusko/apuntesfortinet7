# Laboratorio Fortinet 7.0
Estos son apuntes personales del curso Fortinet 7: De principiante a experto, impartido por Eduardo Nazareno Anselmi [Adquirir el curso aquí](https://www.udemy.com/course/fortinet-7-de-principiante-a-experto/)

En este curso se require los siguientes programas para el laboratorio:
* [GNS3](https://gns3.com/software) descargar la versión servidor y cliente

* [VMWARE](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html)

Una vez descargado los programas vamos a configurar el Vmware:

Para conectarnos al GNS3 y que nos permita jalar el laboratorio tenemos que copiar los datos de la ip que en este caso es la 192.168.1.166 y al puerto 80, el usuario es gns3 al igual que el password

![0](1.png)

![1](2.png)


Lo primero que vamos hacer es acomador los equipos superpuestos y a cambia el icono de los equipos fortinet para hacer eso presionamos botón derecho del mouse y click en cambiar simbolos.

![2](3.png)

Después seleccionar usar simbolo personalizado y click en examinar, seleccionamos el icono y click en aplicar y ok, tenemos que hacer lo cambios en todos los dispositivos que se muestran en el laboratorio(LB)

![3](4.png)

una vez aplicado los cambios tenemos que cerrar y volver abrir el LB y click en start para arrancar todos los dispositivos.

![4](5.png)

Cambiar IP DHCP por una estatica:
Abrimos GNS3 luego click en ok y nos dirigimos a la sección de Network y nos pedirá reiniciar una vez echo los cambios, presionamos yes

![5](6.png)

Ahora de donde dice #networ hacía abajo vamos a quitar el comentario y remplazar la iP y el gateway:

![6](7.png)

Debemos respetar la identación, y una vez hecho los cambios con cntrl + x para salir y click en yes, y nos saldrá un cuadro de dialogo que nos indica que se modificara el archivo presionamos enter

![7](8.png)

Una vez que se reinicio el equipo nos aparecerá la modificación y en el GNS 3 también tenemos que modicar la ip de conexión en edición->Preference->Server

![8](9.png)

Snapshots
Se realiza con los equipos FGT apagados, vamos a edit-> manangment spanshots, click en crear 

Es importante crearlos en cada modificación importante, ya que en caso de borrar algún dispositivo forti podremos restaurarlo.

Y para restauralo son los mismos pasos excepto que en vez de presionar crear, debemos presionar restore

![9](10.png)

Temas que se van a ver en esta primera parte:
![10](11.png)

Primer acceso a nuestro firewall:
![11](12.png)

Nos vamos a conectar a un equipo fortinet, usuario:admin y password:(blank), nos pedirá que cambiemos la contraseña:
![12](13.png)

Primeros comandos:
Tenemos que ingresar a ver la ip del equipo:

FortiGate-VM64-KVM # get system interface
== [ port1 ]
name: port1   mode: dhcp    ip: 192.168.1.170 255.255.255.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port2 ]
== [ port3 ]
== [ port4 ]

*Después veremos la configuración del equip
FortiGate-VM64-KVM # conf sys interface

*Ahora vamos activar http para ingresar a través de la web
FortiGate-VM64-KVM (interface) # edit port1

FortiGate-VM64-KVM (port1) # show
config system interface
    edit "port1"
        set vdom "root"
        set mode dhcp
        set allowaccess ping https ssh fgfm
        set type physical
        set snmp-index 1
    next
end

*Con este puerto se permite el acceso a través del http
FortiGate-VM64-KVM (port1) # append allowaccess http

* show para confirmar que el puerto este activo
FortiGate-VM64-KVM (port1) # show
config system interface
    edit "port1"
        set vdom "root"
        set mode dhcp
        set allowaccess ping https ssh http fgfm
        set type physical
        set snmp-index 1
    next
end

*Para guardar cambios con end
FortiGate-VM64-KVM (port1) # end

Comprobamos con el navegador, ingresamos el usuario y contraseña
![13](14.png)

Opcional: En este caso seleccione begin
![14](15.png)

Nombramos el equipo como SITE-A y click en ok
![15](16.png)

Seleccionamos Optimal y click en ok
![16](17.png)

Dashboard de inicio
![17](18.png)

Configuramos de la misma manera el SITE-B.

En ambos equipos Fortigate el puerto 1 será para la administración y el puerto 2 y 3 similarán ser direcciones IP Pueblicas (ISP) y el puerto 4 será la LAN de las Pc's.

Ahora si hacemos un ping a google a través del CLI que se encuentra en el dashboard en ambos equipos nos dará salida:

SITE-A # exec ping google.com
PING google.com (142.251.34.206): 56 data bytes
64 bytes from 142.251.34.206: icmp_seq=0 ttl=117 time=13.4 ms
SITE-B # exec ping google.com

¿Por qué pasa esto si aún no hemos configurado? (laboratorio)se debe a que el puerto 1 tiene configuración DHCP

Para ello vamos a ingresar vía web a uno de los forti y nos vamos a Network-> Interfaces y vermos que ahí está tomando la DNS configurado en nuestro router, entonces tenemos que deshabilitar la opción que dice Rerieve default gateway from server y la que dice override Internal DNS, 
![18](19.png)

Ahora si volver hacer ping ya no nos permitira hacer ping y está bien porqué el puerto uno es para la configuración y administración.

admin@192.168.1.170's password:
SITE-A # exec ping google.com
Unable to resolve hostname.

SITE-A #

Ahora vamos a ingresar a configurar la zona horaria a través de la interfaz gráfica:
System->Settings 

En esta misma sección tenemos que darle el máximo tiempo de conexión antes de que se cierre la sesión que es de 480 minutos(solo se aplica en laboratorio)

Idle timeout : 480

Auto file system check: check
(esto en caso de que se apague mal el equipo)

Ahora vamos hacer una pequeña revisión:
![19](20.png)

¿Que és un dashboard?
Colección de widget, encontraremos información del sistema, estado de licencias
¿Qué es un monitor?
nos muestra tráfico o destino, cosas que el forti está procesando o preceso, es como ver un widget

En el menú en la parte de NEtwork: no serán vistas en el curso debido a que no es el scope del curso, ya que no se toman en cuentan las certificaciones.
RIP,OSPF,BGP,Routing Objets, Multicas

Vamos a empezar poniendo una IP fija a nuestros dos dispositivos forti

1.- Forma web
Una vez ingresando a nuestro dispositivo vamos a network->interfaces->Port1->seleccionamos la opción manual y le ponemos la ip que deseamos.

![20](21.png)

Dejaré los mismos que me dió por default y click en ok

![21](22.png)

2.- Ahora a través de la consola con el comando set ip 192.168.1.6/24
```
edit port
set mode static
show
set ip ip/ip mask
show
end
```
```
SITE-B (interface) # edit port1

SITE-B (port1) # show
config system interface
    edit "port1"
        set vdom "root"
        set mode dhcp
        set allowaccess ping https ssh http fgfm
        set type physical
        set snmp-index 1
        config ipv6
            set ip6-send-adv enable
            set ip6-other-flag enable
        end
        set defaultgw disable
        set dns-server-override disable
    next
end

SITE-B (port1) # set mode static

SITE-B (port1) # show
config system interface
    edit "port1"
        set vdom "root"
        set ip 192.168.1.172 255.255.255.0
        set allowaccess ping https ssh http fgfm
        set type physical
        set snmp-index 1
        config ipv6
            set ip6-send-adv enable
            set ip6-other-flag enable
        end
        set dns-server-override disable
    next
end

SITE-B (port1) # set ip 192.168.1.6

incomplete command in the end
Command fail. Return code -160

SITE-B (port1) # set ip 192.168.1.6/24

SITE-B (port1) # show
config system interface
    edit "port1"
        set vdom "root"
        set ip 192.168.1.6 255.255.255.0
        set allowaccess ping https ssh http fgfm
        set type physical
        set snmp-index 1
        config ipv6
            set ip6-send-adv enable
            set ip6-other-flag enable
        end
        set dns-server-override disable
    next
end

SITE-B (port1) #
```

Con get system status, revisamos cuando vencen las licencias.

```

SITE-A # get system status
Version: FortiGate-VM64-KVM v7.0.3,build0237,211207 (GA)
Virus-DB: 1.00000(2018-04-09 18:07)
Extended DB: 1.00000(2018-04-09 18:07)
Extreme DB: 1.00000(2018-04-09 18:07)
AV AI/ML Model: 0.00000(2001-01-01 00:00)
IPS-DB: 6.00741(2015-12-01 02:30)
IPS-ETDB: 6.00741(2015-12-01 02:30)
APP-DB: 6.00741(2015-12-01 02:30)
INDUSTRIAL-DB: 6.00741(2015-12-01 02:30)
IPS Malicious URL Database: 1.00001(2015-01-01 01:01)
Serial-Number: FGVMEV3NOACKGWED
License Status: Valid
Evaluation License Expires: Thu Oct  6 07:11:01 2022
VM Resources: 1 CPU/1 allowed, 997 MB RAM/2048 MB allowed
Log hard disk: Available
Hostname: SITE-A
Operation Mode: NAT
Current virtual domain: root
Max number of virtual domains: 1
Virtual domains status: 1 in NAT mode, 0 in TP mode
Virtual domain configuration: disable
FIPS-CC mode: disable
Current HA mode: standalone
Branch point: 0237
Release Version Information: GA
FortiOS x86-64: Yes
System time: Mon Sep 26 15:29:21 2022
Last reboot reason: warm reboot
```

## Configuración de interfaces

Primero vamos a trabajar con el Site A.
Puerto 2 sera nuestro ISP1 (La IP es la que nos muestra en el laboratorio)

![22](23.png)

Puerto 3 (La IP es la que nos muestra en el laboratorio)

![23](24.png)

Ahora para probar hacemos un ping al gateway 
al router del microtic del laboratorio
```
SITE-A # exec ping 180.45.22.2
PING 180.45.22.2 (180.45.22.2): 56 data bytes
64 bytes from 180.45.22.2: icmp_seq=0 ttl=64 time=8.3 ms
64 bytes from 180.45.22.2: icmp_seq=1 ttl=64 time=3.7 ms
64 bytes from 180.45.22.2: icmp_seq=2 ttl=64 time=3.3 ms
64 bytes from 180.45.22.2: icmp_seq=3 ttl=64 time=3.3 ms
64 bytes from 180.45.22.2: icmp_seq=4 ttl=64 time=3.8 ms

--- 180.45.22.2 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 3.3/4.4/8.3 ms

```
```

SITE-A # exec ping 200.212.31.2
PING 200.212.31.2 (200.212.31.2): 56 data bytes
64 bytes from 200.212.31.2: icmp_seq=0 ttl=64 time=8.4 ms
64 bytes from 200.212.31.2: icmp_seq=1 ttl=64 time=3.4 ms
64 bytes from 200.212.31.2: icmp_seq=2 ttl=64 time=5.5 ms
64 bytes from 200.212.31.2: icmp_seq=3 ttl=64 time=4.0 ms
64 bytes from 200.212.31.2: icmp_seq=4 ttl=64 time=2.3 ms

--- 200.212.31.2 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 2.3/4.7/8.4 ms

```
Ahora si tratamos de realizar un ping a google nos saldra que no es posible, esto es debido a que nos falta la default route, y esto aplica para cualquier router no importa la marca.

```
SITE-A # ping google.com
Unknown action 0
```
Para eso nos vamos a Network->static route->create new
![24](25.png)

Como podemos ver es agregar el gateway (sin mascara)de los proveedores anteriormente añadidos
![25](26.png)

En esta segunda configuración en la opción de distancia la ponemos más alta, eso se debe a que uno será la red principal(10) y la otra (15) el backup en caso de que se caiga o falle la red entra la otra en función. 
![26](27.png)

Ahora si hacemos nuevamente ping, podremos salir a internet:

```
SITE-A # exec ping google.com
PING google.com (142.250.72.174): 56 data bytes
64 bytes from 142.250.72.174: icmp_seq=0 ttl=109 time=90.1 ms
64 bytes from 142.250.72.174: icmp_seq=1 ttl=109 time=62.2 ms
64 bytes from 142.250.72.174: icmp_seq=2 ttl=109 time=60.6 ms
64 bytes from 142.250.72.174: icmp_seq=3 ttl=109 time=61.5 ms
64 bytes from 142.250.72.174: icmp_seq=4 ttl=109 time=62.0 ms

--- google.com ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 60.6/67.2/90.1 ms
```

Ahora vamos a configurar el puerto 4.

Agregamos la IP y habilitamos el ping

![27](28.png)

Ahora nos conectamos a la pc y hacemos la pruba de ping al 10.0.1.254

![28](29.png)

![29](30.png)

Ahora vamos a repetir los mimos pasos anteriores para el forti SITE-B pero en este caso a través de la terminal.

Configuración puerto 2
```
SITE-B # confi sys interface


SITE-B (interface) # edit port2

SITE-B (port2) # show
config system interface
    edit "port2"
        set vdom "root"
        set type physical
        set snmp-index 2
    next
end

SITE-B (port2) # set ip 69.89.123.1/30

SITE-B (port2) # set alias ISP1

SITE-B (port2) # set role wan

SITE-B (port2) # show
config system interface
    edit "port2"
        set vdom "root"
        set ip 69.89.123.1 255.255.255.252
        set type physical
        set alias "ISP1"
        set role wan
        set snmp-index 2
    next
end

SITE-B (port2) # end
```

Ahora el puerto 3 

```

SITE-B (interface) # edit port3

SITE-B (port3) # show
config system interface
    edit "port3"
        set vdom "root"
        set type physical
        set snmp-index 3
    next
end


SITE-B (port3) # set ip 45.32.12.1/30

SITE-B (port3) # set alias ISP-2

SITE-B (port3) # set role wan

SITE-B (port3) # show
config system interface
    edit "port3"
        set vdom "root"
        set ip 45.32.12.1 255.255.255.252
        set type physical
        set alias "ISP-2"
        set role wan
        set snmp-index 3
    next
end

SITE-B (port3) # end {nota: en caso de seguir configurando en vez de usar end podemos usar next para no salir de la configuración}

```
SITE-B (interface) # edit port4

SITE-B (port4) # show
config system interface
    edit "port4"
        set vdom "root"
        set type physical
        set snmp-index 4
    next
end

SITE-B (port4) # set ip 10.0.2.254/24

SITE-B (port4) # show
config system interface
    edit "port4"
        set vdom "root"
        set ip 10.0.2.254 255.255.255.0
        set type physical
        set snmp-index 4
    next
end

SITE-B (port4) # set allowaccess ping

SITE-B (port4) # set alias lan

SITE-B (port4) # set role lan

SITE-B (port4) # show
config system interface
    edit "port4"
        set vdom "root"
        set ip 10.0.2.254 255.255.255.0
        set allowaccess ping
        set type physical
        set alias "lan"
        set role lan
        set snmp-index 4
    next
end

SITE-B (port4) # end

``
Ahora colocaremos las rutas

Ya que si tratamos de hacer un ping no nos dejará
```
SITE-B # ping google.com
Unknown action 0
```
ISP 1
```
SITE-B # config router static

SITE-B (static) # show
config router static
end

SITE-B (static) # edit 1
new entry '1' added

SITE-B (1) # show
config router static
    edit 1
    next
end

SITE-B (1) # get
seq-num             : 1
status              : enable
dst                 : 0.0.0.0 0.0.0.0
gateway             : 0.0.0.0
distance            : 10
weight              : 0
priority            : 0
device              :
comment             :
blackhole           : disable
dynamic-gateway     : disable
sdwan-zone          :
dstaddr             :
internet-service    : 0
internet-service-custom:
link-monitor-exempt : disable
bfd                 : disable


SITE-B (1) # set gateway 60.89.123.2

SITE-B (1) # set device port2

SITE-B (1) # show
config router static
    edit 1
        set gateway 69.89.123.2
        set device "port2"
    next
end

SITE-B (1) # next

```
ISP 2

```

SITE-B (1) # set gateway 60.89.123.2

SITE-B (1) # set device port2

SITE-B (1) # show
config router static
    edit 1
        set gateway 69.89.123.2
        set device "port2"
    next
end

SITE-B (1) # next

SITE-B (static) # end

SITE-B # conf router static

SITE-B (static) # edit 2
new entry '2' added

SITE-B (2) # show
config router static
    edit 2
    next
end

SITE-B (2) # set gateway 45.32.12.2

SITE-B (2) # set device port3

SITE-B (2) # set distance 15

SITE-B (2) # show
config router static
    edit 2
        set gateway 45.32.12.2
        set distance 15
        set device "port3"
    next
end

SITE-B (2) # end

```
Con show full-configuration podemos ver todos los valores por defecto y todos los que hemos modificado

```
SITE-B # show full-configuration
#config-version=FGVMK6-7.0.3-FW-build0237-211207:opmode=1:vdom=0:user=admin
#conf_file_ver=282647502847925
#buildno=0237
#global_vdom=1
config system global
    set admin-concurrent enable
    set admin-console-timeout 0
    set admin-forticloud-sso-login disable
    set admin-https-pki-required disable
    unset admin-https-ssl-banned-ciphers
    set admin-https-ssl-ciphersuites TLS-AES-128-GCM-SHA256 TLS-AES-256-GCM-SHA384 TLS-CHACHA20-POLY1305-SHA256
    set admin-https-ssl-versions tlsv1-2
    set admin-lockout-duration 60
    set admin-lockout-threshold 3
    set admin-login-max 100
    set admin-maintainer enable
    set admin-port 80
    set admin-restrict-local disable
    set admin-scp disable
    set admin-server-cert "self-sign"
    set admin-sport 443
    set admin-ssh-grace-time 120
    set admin-ssh-password enable
    set admin-ssh-port 22
    set admin-ssh-v1 disable
    set admin-telnet enable
    set admin-telnet-port 23
    set admintimeout 448
    set alias "FortiGate-VM64-KVM"
    set allow-traffic-redirect enable
    set anti-replay strict
    set arp-max-entry 131072
    set auth-cert "self-sign"
    set auth-http-port 1000
    set auth-https-port 1003
    set auth-keepalive disable
    set auth-session-limit block-new
    set auto-auth-extension-device enable
    set autorun-log-fsck disable
    set av-affinity "0"
    set av-failopen pass
    set av-failopen-session disable
    set batch-cmdb enable
    set block-session-timer 30
    set br-fdb-max-entry 8192
    set cert-chain-max 8
    set cfg-save automatic
    set check-protocol-header loose
    set check-reset-range disable
    set cli-audit-log disable
    set cloud-communication enable

```

```
SITE-B # conf rout static

SITE-B (static) # show
config router static
    edit 1
        set gateway 69.89.123.2
        set device "port2"
    next
    edit 2
        set gateway 45.32.12.2
        set distance 15
        set device "port3"
    next
end


```

Hacemos un ping a google para rectificar

```
SITE-B # exec ping google.com
PING google.com (142.251.34.142): 56 data bytes
64 bytes from 142.251.34.142: icmp_seq=0 ttl=111 time=11.9 ms
64 bytes from 142.251.34.142: icmp_seq=1 ttl=111 time=11.0 ms
64 bytes from 142.251.34.142: icmp_seq=2 ttl=111 time=11.3 ms
64 bytes from 142.251.34.142: icmp_seq=3 ttl=111 time=20.4 ms
64 bytes from 142.251.34.142: icmp_seq=4 ttl=111 time=13.8 ms

--- google.com ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 11.0/13.6/20.4 ms

SITE-B #

Y un ping a la pc-2

SITE-B # exec ping 10.0.2.10
PING 10.0.2.10 (10.0.2.10): 56 data bytes
64 bytes from 10.0.2.10: icmp_seq=0 ttl=64 time=2.8 ms
64 bytes from 10.0.2.10: icmp_seq=1 ttl=64 time=1.9 ms
64 bytes from 10.0.2.10: icmp_seq=2 ttl=64 time=3.6 ms
64 bytes from 10.0.2.10: icmp_seq=3 ttl=64 time=4.7 ms
64 bytes from 10.0.2.10: icmp_seq=4 ttl=64 time=1.9 ms

--- 10.0.2.10 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 1.9/2.9/4.7 ms

```

## Backups & Restore

### Opción web

Ir a configuración, luego backup y si se requiere, podemos restaurar.
![29](31.png)

En caso de querer editar el archivo backup para pasarlo a otro equipo similar de dónde se realizado la copia debemos tener en cuenta, la cantidad de puertos y la versión del firewall tiene que ser el mismo del equipo original.

```
#config-version= FGVMK6-7.0.3-FW-build0237-211207:opmode=0:vdom=0:user=admin

#conf_file_ver=282763466964943
#buildno=0237
#global_vdom=1
config system global
    set alias "FortiGate-VM64-KVM"
    set hostname "SITE-A"
    set timezone 04
end
```
## Ahora a traves de la linea de comandos

Lista de opción con ? 
```
SITE-B # execute backup ?
config           config
disk             disk
full-config      full-config
ipsuserdefsig    ipsuserdefsig
memory           memory
```

### Con el siguiente comando respaldamos a través de un servidor FTP

***excute backup config ftp "Nombre que asiganaremos al archivo" "IP del servidor FTP" "Usuario" "Password"***

```
SITE-B # execute backup config ftp SITE-B-27092022 200.212.31.2 ftp ftp
Please wait...

Connect to ftp server 200.212.31.2 ...
Send config file to ftp server OK.

```

### Con siguiente comando resturamos el backup con el comando restore


***excute restore config ftp "Nombre del archivo" "IP del servidor FTP" "Usuario" "Password"***

Al hacer esto el equipo se va reiniciar automáticamente
```
SITE-B # execute restore config ftp SITE-B-27092022 200.212.31.2 ftp ftp
This operation will overwrite the current setting and could possibly reboot the system!
Do you want to continue? (y/n)y

Please wait...

Connect to ftp server 200.212.31.2 ...
Get config file from ftp server OK.
File check OK.
```
Si uno tiene dudas si se reinició o no el equipo el comando para revisar es el siguiente
```
SITE-B # get system performance status
CPU states: 0% user 2% system 0% nice 94% idle 1% iowait 3% irq 0% softirq
CPU0 states: 0% user 2% system 0% nice 94% idle 1% iowait 3% irq 0% softirq
Memory: 1021528k total, 714496k used (69.9%), 163128k free (16.0%), 143904k freeable (14.1%)
Average network usage: 1 / 0 kbps in 1 minute, 1 / 0 kbps in 10 minutes, 0 / 0 kbps in 30 minutes
Average sessions: 14 sessions in 1 minute, 17 sessions in 10 minutes, 12 sessions in 30 minutes
Average session setup rate: 0 sessions per second in last 1 minute, 0 sessions per second in last 10 minutes, 0 sessions per second in last 30 minutes
Virus caught: 0 total in 1 minute
IPS attacks blocked: 0 total in 1 minute
Uptime: 0 days,  0 hours,  5 minutes

```
### Renovar licencia de laboratorio, en la vida real se contrata un partner.

En este caso como es laboratorio tenemos que revisar cuando vence la experiencia para renovarlo, para ello realice un respaldo antes.

En este caso vence el 6 de octubre
```
admin@192.168.1.170's password:
SITE-A # execute backup config ftp sitea2809 200.212.31.2 ftp ftp
Please wait...

Connect to ftp server 200.212.31.2 ...
Send config file to ftp server OK.

SITE-A # get sys status
Version: FortiGate-VM64-KVM v7.0.3,build0237,211207 
License Status: Valid
Evaluation License Expires: Thu Oct  6 07:11:01 2022
VM Resources: 1 CPU/1 allowed, 997 MB RAM/2048 MB 
```
#### Con este comando reiniciamos a default el equipo FTG de su caja pero solo en la versión de laboratorio permite restaurar las licencias otros 15 días más de prueba
En este caso tendremos que configurar de nuevo la configuración de interfaces una vez que restauremos el backup.
```
SITE-A # execute factoryreset
```
#### Pero si ejectamos este otro comando nos permite restaurar el equipo a default pero con la excepción de que no borra la configuración de la red.

```
SITE-A # execute factoryreset2
```
#### ejecutamos comando, tener en cuenta que tardará unos minutos.
```
SITE-A # exec factoryreset2
This operation will reset the system to factory default except system.global.vdom-mode/system.global.long-vdom-name/VDOMs/system.virtual-switch/system.interface/system.settings/router.static/router.static6!
Do you want to continue? (y/n)y
```

Si nos volvemos a conectar a través de la terminal no pedira nuevamente que coloquemos una contraseña nueva
```
login as: admin
You are forced to change your password. Please input a new password.
```
Si revisamos de nuevo el equipo veremos que el nombre del eequipo cambió así como la fecha de fin de licencia y que los puertos configurados continuan.

```
FortiGate-VM64-KVM # get syst stat
Version: FortiGate-VM64-KVM v7.0.3,build0237,211207 (GA)
Evaluation License Expires: Thu Oct 13 12:33:32 2022
System time: Wed Sep 28 12:43:39 2022
Last reboot reason: warm reboot
```

Si revisamos la configuración de los puertos vermos que continuán gracias a que fue un exec resetfactory2

```

FortiGate-VM64-KVM # show syst inte
config system interface
    edit "port1"
        set vdom "root"
        set ip 192.168.1.170 255.255.255.0
        set allowaccess ping https ssh http fgfm
        set type physical
        set alias "MGMT"
        set device-identification enable
        set lldp-transmission enable
        set role lan
        set snmp-index 1
        config ipv6
            set ip6-send-adv enable
            set ip6-other-flag enable
        end
        set dns-server-override disable
    next
    edit "port2"
        set vdom "root"
        set ip 200.212.31.1 255.255.255.252
        set type physical
        set alias "ISP1"
        set lldp-reception enable
        set role wan
        set snmp-index 2
        config ipv6
            set ip6-send-adv enable
            set ip6-other-flag enable
        end
    next
    edit "port3"
        set vdom "root"
        set ip 180.45.22.1 255.255.255.252
        set type physical
        set alias "ISP2"
        set lldp-reception enable
        set role wan
        set snmp-index 3
        config ipv6
            set ip6-send-adv enable
            set ip6-other-flag enable
        end
    next
    edit "port4"
        set vdom "root"
        set ip 10.0.1.254 255.255.255.0
        set allowaccess ping
        set type physical
        set alias "LAN"
        set device-identification enable
--More--

```

Ahora vamos a restaurar el respaldo antes realizado.

```
FortiGate-VM64-KVM # exec rest conf ftp sita2809 200.212.31.2 ftp ftp
This operation will overwrite the current setting and could possibly reboot the system!
Do you want to continue? (y/n)

```

### Revisiones

Son Backups locales guardadas en los equipos de FTG, no es recomendable debido a que si falla el equipo y no almacenamos externamente podemos perder el backup.

## Aprendiendo a usar CLI
? si apretamos nos mostrará el menú

SITE-A #
config      Configure object.
get         Get dynamic and system information.
show        Show configuration.
diagnose    Diagnose facility.
execute     Execute static commands.
alias       Execute alias commands.
exit        Exit the CLI.

Aquí podemos hacer una busqueda por filtro, donde grep es quien recibe la sentencia y -f es el filtro
```
SITE-A # show syst int | grep -f "port1"
config system interface
    edit "port1" <---
        set vdom "root"
        set ip 192.168.1.170 255.255.255.0
        set allowaccess ping https ssh http fgfm
        set type physical
        set alias "MGMT"
        set device-identification enable
        set lldp-transmission enable
        set role lan
        set snmp-index 1
        set dns-server-override disable
    next
    edit "port10" <---
        set vdom "root"
        set type physical
        set snmp-index 10
    next
end
```
otro ejemplo pero ahora con filtro y el insensitive
```
SITE-A # show | grep -f "Sitea"

SITE-A # show | grep -f "SITE-A"
config system global
    set alias "FortiGate-VM64-KVM"
    set hostname "SITE-A" <---
    set timezone 04
end
```
En este ejemplo podemos ver que se agrega la opción -i que indica que es insensitive, por lo tanto no importa si lo busco con mayusculas o minúsculas
```
SITE-A (global) # get | grep -i "HOST"
gui-allow-default-hostname: disable
gui-display-hostname: disable
hostname            : SITE-A
```
```
SITE-A # show | grep -fi "vpn"
config system accprofile
    edit "prof_admin"
        set secfabgrp read-write
        set ftviewgrp read-write
        set authgrp read-write
        set sysgrp read-write
        set netgrp read-write
        set loggrp read-write
        set fwgrp read-write
        set vpngrp read-write <---
        set utmgrp read-write
        set wanoptgrp read-write
        set wifi read-write
    next
end
config system interface
    edit "ssl.root"
        set vdom "root"
        set type tunnel
        set alias "SSL VPN interface" <---
        set snmp-index 13
    next
end
config system replacemsg sslvpn "sslvpn-login" <---
end
config system replacemsg sslvpn "sslvpn-header" <---
end
config system replacemsg sslvpn "sslvpn-limit" <---
end
config system replacemsg sslvpn "hostcheck-error" <---
end
config system replacemsg sslvpn "sslvpn-provision-user" <---
end
config system replacemsg sslvpn "sslvpn-provision-user-sms" <---
end
config firewall address
    edit "SSLVPN_TUNNEL_ADDR1" <---
        set uuid 3b4c13ee-39b7-51ed-2d2b-35f95759a991
        set type iprange
        set start-ip 10.212.134.200
        set end-ip 10.212.134.210
    next
end
config firewall address6
    edit "SSLVPN_TUNNEL_IPv6_ADDR1" <---
        set uuid 3b4c1560-39b7-51ed-b4ee-9c4b5cc838c5
        set ip6 fdff:ffff::/120
    next
end
config vpn certificate ca <---
end
config vpn certificate local <---
end
config vpn ssl web host-check-software <---
end
config vpn ssl web portal <---
    edit "full-access"
        set tunnel-mode enable
        set ipv6-tunnel-mode enable
        set web-mode enable
        set ip-pools "SSLVPN_TUNNEL_ADDR1" <---
        set ipv6-pools "SSLVPN_TUNNEL_IPv6_ADDR1" <---
    next
end
config vpn ssl settings <---
    set servercert ''
    set port 443
end
config vpn ocvpn <---
end
config report layout
    edit "default"
        set title "FortiGate System Analysis Report"
        set style-theme "default-report"
        set options include-table-of-content view-chart-as-heading
        config body-item
            edit 1701
                set text-component heading1
                set content "VPN Usage" <---
            next
            edit 1711
                set type chart
                set top-n 80
                set chart "vpn.bandwidth.static-tunnels_c" <---
            next
            edit 1721
                set type chart
                set top-n 80
                set chart "vpn.bandwidth.dynamic-tunnels_c" <---
            next
            edit 1731
                set type chart
                set top-n 80
                set chart "vpn.bandwidth.ssl-tunnel.users_c" <---
            next
            edit 1741
                set type chart
                set top-n 80
                set chart "vpn.bandwidth.ssl-web.users_c" <---
            next
        end
    next
end

```
## Gestionando administradores
Aquí vamos administrar los permisos y crear usuarios
System-> administrators
System-> admin profiles
![31](32.png)

## Nuestra primer policy vía web
Estos pasos se realizan en ambos dispositivos forti

Policy objets->Firewall Policy
![32](33.png)
Como vemos ha sido creado
![33](34.png)

Ahora si hacemos un ping en la pc1 vemos que ahora si lo hace:
![34](35.png)

## Nuestra primer policy vía CLI
Estos pasos se realizan en ambos dispositivos forti

```
login as: admin
admin@192.168.1.170's password:

SITE-A # config firewall policy

SITE-A (policy) # show
config firewall policy
end

SITE-A (policy) # show full-configuration
config firewall policy
end
```
Aquí vamos a crear el primer Policy
```
SITE-A (policy) # edit 1
new entry '1' added
```
Aquí vamos hacer un show y get para obtener información de como se encuentra el policy antes de ponerle el nombre, el origine, destino, el servicio
```
SITE-A (1) # show
config firewall policy
    edit 1
        set uuid eed54a0e-4a4f-51ed-b12a-4affe695a569
    next
end

SITE-A (1) # get
policyid            : 1
status              : enable
name                :
uuid                : eed54a0e-4a4f-51ed-b12a-4affe695a569
srcintf             :
dstintf             :
action              : deny
ztna-status         : disable
srcaddr             :
dstaddr             :
srcaddr6            :
dstaddr6            :
internet-service    : disable
internet-service-src: disable
reputation-minimum  : 0
src-vendor-mac      :
rtp-nat             : disable
schedule            :
schedule-timeout    : disable
service             :
tos-mask            : 0x00
anti-replay         : enable
logtraffic          : utm
logtraffic-start    : disable
session-ttl         : 0
vlan-cos-fwd        : 255
vlan-cos-rev        : 255
fec                 : disable
wccp                : disable
groups              :
users               :
fsso-groups         :
natip               : 0.0.0.0 0.0.0.0
tcp-mss-sender      : 0
tcp-mss-receiver    : 0
comments            :
block-notification  : disable
custom-log-fields   :
replacemsg-override-group:
srcaddr-negate      : disable
dstaddr-negate      : disable
service-negate      : disable
captive-portal-exempt: disable
dsri                : disable
radius-mac-auth-bypass: disable
delay-tcp-npu-session: disable
vlan-filter         :
sgt-check           : disable
send-deny-packet    : disable
match-vip           : disable
```
Ahora si vamos a ponerle un nombre
```
SITE-A (1) # set name Internet
```
Ahora el Incoming Interface en este caso es el Puerto 4
```
SITE-A (1) # set srcintf port4
```
Después el Outgoing Interface en este caso es el Puerto 3
```
SITE-A (1) # set dstintf port3
```
Ahora vamos a poner all la dirección de origen, destino, la agenda y servicio que este último tiene que ser en mayúscula
```
SITE-A (1) # set srcaddr all

SITE-A (1) # set dstaddr all

SITE-A (1) # set schedule always

SITE-A (1) # set service ALL
```
Ahora vamos a observar los cambios con un show y un show full-fullconfiguration
```
SITE-A (1) # show
config firewall policy
    edit 1
        set name "Internet"
        set uuid eed54a0e-4a4f-51ed-b12a-4affe695a569
        set srcintf "port4"
        set dstintf "port3"
        set srcaddr "all"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"
    next
end

SITE-A (1) # show full-configuration
config firewall policy
    edit 1
        set status enable
        set name "Internet"
        set uuid eed54a0e-4a4f-51ed-b12a-4affe695a569
        set srcintf "port4"
        set dstintf "port3"
```
Aquí podemos observar que  la opción de actión está como denegado y no aparece la opción de NAT en la lista
```
        set action deny

```        
        set ztna-status disable
        set srcaddr "all"
        set dstaddr "all"
        set internet-service disable
        set internet-service-src disable
        unset reputation-minimum
        set rtp-nat disable
        set schedule "always"
        set schedule-timeout disable
        set service "ALL"
        set tos-mask 0x00
        set anti-replay enable
        set logtraffic utm
        set logtraffic-start disable
        set session-ttl 0
        set vlan-cos-fwd 255
        set vlan-cos-rev 255
        set fec disable
        set wccp disable
        set natip 0.0.0.0 0.0.0.0
        set tcp-mss-sender 0
        set tcp-mss-receiver 0
        set comments ''
        set block-notification disable
        set replacemsg-override-group ''
        set srcaddr-negate disable
        set dstaddr-negate disable
        set service-negate disable
        set captive-portal-exempt disable
        set dsri disable
        set radius-mac-auth-bypass disable
        set delay-tcp-npu-session disable
        unset vlan-filter
        set send-deny-packet disable
        set match-vip disable
    next
end
```

Aquí vamos a poner la opción Action en modo ACCEPT (lowcase) y 
```
SITE-A (1) # set action accept
```
De nuevo hacemos un show full y aquí podremos ver como la opción NAT aparece en la lista
```
SITE-A (1) # show full-configuration
config firewall policy
    edit 1
        set status enable
        set name "Internet"
        set uuid eed54a0e-4a4f-51ed-b12a-4affe695a569
        set srcintf "port4"
        set dstintf "port3"
```
        set action accept
        set nat64 disable
        set nat46 disable
```      
        set ztna-status disable
        set srcaddr "all"
        set dstaddr "all"
        set internet-service disable
        set internet-service-src disable
        unset reputation-minimum
        set rtp-nat disable
        set schedule "always"
        set schedule-timeout disable
        set service "ALL"
        set tos-mask 0x00
        set anti-replay enable
        set dynamic-shaping disable
        set passive-wan-health-measurement disable
        set utm-status disable
        set inspection-mode flow
        set profile-protocol-options "default"
        set ssl-ssh-profile "no-inspection"
        set logtraffic utm
        set logtraffic-start disable
        set capture-packet disable
        set auto-asic-offload enable
        set nat disable
        set session-ttl 0
        set vlan-cos-fwd 255
        set vlan-cos-rev 255
        set fec disable
        set wccp disable
        set disclaimer disable
        set email-collect disable
        set natip 0.0.0.0 0.0.0.0
        set diffserv-forward disable
        set diffserv-reverse disable
        set tcp-mss-sender 0
        set tcp-mss-receiver 0
        set comments ''
        set block-notification disable
        set replacemsg-override-group ''
        set srcaddr-negate disable
        set dstaddr-negate disable
        set service-negate disable
        set timeout-send-rst disable
        set captive-portal-exempt disable
        set dsri disable
        set radius-mac-auth-bypass disable
        set delay-tcp-npu-session disable
        unset vlan-filter
        set traffic-shaper ''
        set traffic-shaper-reverse ''
        set per-ip-shaper ''
    next 
    end

Habilitamos el NAT
```
SITE-A (1) # set nat enable
```
Volvemos a hacer un show full

```
SITE-A (1) # show full-configuration
config firewall policy
    edit 1
        set status enable
        set name "Internet"
        set uuid eed54a0e-4a4f-51ed-b12a-4affe695a569
        set srcintf "port4"
        set dstintf "port3"
        set action accept
        set ztna-status disable
        set srcaddr "all"
        set dstaddr "all"
        set internet-service disable
        set internet-service-src disable
        unset reputation-minimum
        set rtp-nat disable
        set schedule "always"
        set schedule-timeout disable
        set service "ALL"
        set tos-mask 0x00
        set anti-replay enable
        set dynamic-shaping disable
        set passive-wan-health-measurement disable
        set utm-status disable
        set inspection-mode flow
        set profile-protocol-options "default"
        set ssl-ssh-profile "no-inspection"
        set logtraffic utm
        set logtraffic-start disable
        set capture-packet disable
        set auto-asic-offload enable
        set nat enable
        set permit-any-host disable
        set permit-stun-host disable
        set fixedport disable
        set ippool disable
        set session-ttl 0
        set vlan-cos-fwd 255
        set vlan-cos-rev 255
        set fec disable
        set wccp disable
        set disclaimer disable
        set email-collect disable
        set natip 0.0.0.0 0.0.0.0
        set diffserv-forward disable
        set diffserv-reverse disable
        set tcp-mss-sender 0
        set tcp-mss-receiver 0
        set comments ''
        set block-notification disable
        set replacemsg-override-group ''
        set srcaddr-negate disable
        set dstaddr-negate disable
        set service-negate disable
        set timeout-send-rst disable
        set captive-portal-exempt disable
        set dsri disable
        set radius-mac-auth-bypass disable
        set delay-tcp-npu-session disable
        unset vlan-filter
        set traffic-shaper ''
        set traffic-shaper-reverse ''
        set per-ip-shaper ''
    next
end

SITE-A (1) # end

SITE-A #

```
Crear objetos de tipo Address, Schedule y service
Esto los encontramos en la opción de Policy & Objects

![35](36.png)

Primero vamos a crear un objeto tipo Address
![36](37.png)
Vamos escoger del tipo subnet en este caso, pero el que me parece interesante es la opción demografica para prohibir conexiones de otros países como Isarel, la India, China
![37](38.png)
En este caso la IP es del servidor del laboratorio  y vemos también que ya se ve en la lista con el nombre Active Directory
![38](39.png)

Vamos a la policy del firewall y en origen seleccionamos el active directory
![39](40.png)

Lo que hace es que el único equipo que va a salir a través de internet será el servidor windows y no la terminal, en este ejemplo cree otro equipo con windows 7 conectado al puerto 5 en vez del 4 entonces al hacer la prueba de ping ya no tiene salida a internet

![40](41.png)

Respecto a la opción de horario se cambia en igual en el policy en vez de alway personalizar el servicio o salida a internet.


## SERVICE
En la opción de Service se encuentra una opción que se llama Web Access que cubre salidas a http, https y la resolución de nombres DNS esto con el fin de poder navegar en internet lo que impide realizar ping pero si navegar a través de internet.

![41](42.png)

![42](43.png)

Otra opción en la parte de service sería seleccionar invidualmente los permisos de salida http, https, DNS.
![43](44.png)

## Schedules
En este ejemplo creamos un schedules para implementar el servicio de internet en horario en el que estará disponible.

![44](45.png)

Aquí lo implementamos en nuestro policy 
![45](46.png)

Esto hará que el servicio de internet en esta computadora o grupo de equipos el servicio de internet se suspenda.

Lo que noté en esta prueba es que funciona con la opción one time y no con la opción Recurrening

![46](47.png)

## Internet Services

La lista servicios que están por default o se pueden crear, sirven para la opción destination en firewall police.

![47](48.png)

Aquí podemos agregar a la lista servicios como teamviewer, telegram y bloquearlo, pero no se recomienda hacerlo desde aquí que para eso Forti tiene opción para bloquear paginas web y servicios, aplicaciones.

![48](49.png)

Ahora con respecto a las firewall policy recordar que arriba debe de ir los de bloqueo y abajo a los de permiso.

![49](50.png)

## Configurar Rutas estáticas

En este ejemplo se van a conectar los dos routers simulado que el Site-A es LEgales con la IP 172.21.0.1/30 y el Site-B 
y hacer un ping de la computadora win7 (site A) al dominio windows server(Site A)

Primero vamos a ir a configurar en el Site B en Network->Static Route->Create New
Se coloca en Destino la IP (10.0.1.0/24) debido a que pertenece al grupo del puerto 4 y como Gateway (172.21.0.1) porque pertenece al grupo de IP de conexión del puerto 7 de FG de Legales.

![50](51.png)

Y haremos lo mismo en el Site A
Se coloca en Destino la IP (10.0.2.0/24) debido a que pertenece al grupo del puerto 4 y como Gateway (172.21.0.2) porque pertenece al grupo de IP de conexión del puerto 7 de FG de Legales.

![51](52.png)

Después necesitamos crear una politica, nos vamos a Policy & Objects->Firewall Policy->Create New, ya que el que teniamos no sirve para la conexión entre routers, servía para la salida a internet.

Primero lo vamos hacer en el Site-B, que la nueva política se llamará TO_LEGALES, se desactiva el NAT debido a que será una conexión device to device y salida a internet.

En este caso solo es de IDA, necesitamos uno de Reversa

![52](53.png)

Y para eso vamos a dar botón derecho encima de TO_LEGALES y Seleccionar la opción que dice REVERSE o hacerlo de forma manual, y nos queda así,

![53](54.png)

Lo mismo se hace para el Site-A y podremos hacer ping en ambas computadoras y obtener comunicación entre ellas

![54](55.png)

Todo funciona bien hasta ahí, pero podemos hacerle unas modificaciones por ejemplo vamos a crear un Address en ambos FG.

Policy & Objects->Adresses->Create New
Esto es muy útil si esta subnet la utilizamos en otros lugares y solo mandarlo a llamar sin necesidad de estar escribiendo esto manualmente.

![55](56.png)

Ahora vamos a rutas estaticas y lo agregamos el objeto de contabilidad

![56](57.png)

Y ahora nos vamos a las politicas de TO/FROM(CONTABILIDAD/LEGALES)
en la opción TO_LEGALES / TO_CONTABILIDAD VA en Destination
Policy & Objects->Firewall Policy y en la opción de destination cambiar all por CONTABILIDAD

![57](58.png)

en la opción FROM_LEGALES / TO_CONTABILIDAD VA en Source
Policy & Objects->Firewall Policy y en la opción de source cambiar all por CONTABILIDAD

![58](59.png)

Ahora vamos a crear un objeto address llamado RED LOCAL
Apuntando a su propia RED de acuerdo al FG, en este caso es el SITE B y la IP es 10.0.2.0/24 en el SITE A sería la IP 10.0.1.0/24
![59](60.png) 

Y volvemos a Firewall Policy y cambiamos el Source y Destination

![60](61.png)

Después de realizar el cambio probamos haciendo ping y funciona correctamente.

![61](62.png)

## Policy Routes
Ahora vamos a realizar las politicas de Enrutamiento, esto no sustituye a las rutas estaticas es un complemento en este caso vamos hacer uso de las rutas estaticas añadidas anteriormente

Aquí podemos indicar son filtros del tipo de tráfico que llega al FG y a que destino queremos enviarlo especificando el protocolo y el puerto.

Network->Policy Routes

En el siguiente ejemplo vamos a mandar datos ICMP a través del puerto 7 con la IP 172.1.0.0/30 y cuando envíe y reciba información http será a través de los puertos de internet Puerto 3 y 4

Esto nos indica esta policy, cuando por el puerto 4 el equipo con la IP 10.0.2.10 quiera mandar info al equipo con la IP 10.0.1.10 contra el puerto TCP 80 haga un forward trafic por el puerto 2 que es el ISP1 con el gateway 60.893123.2 
![62](63.png)

Entonces cuando quiera ir la pc win7 al pc dominio de servicio no lo haga por la ruta estatica con la ip 172.1.0.0/30 sino que lo haga a través del ISP1

## Monitoreo de Rutas
Policy base rute
Rutas producidas por protocolos de enrutamiento dinamico

El monitoreo a trvés de la web es accediendo a Dashboard->Network->doble click en static & Dynamic Routing

Aquí se puede hacer revisión 

![63](64.png)


![64](65.png)

A través del CLI
```

SITE-A # get router info routing-table??
details      show routing table details information
all          show all routing table entries
rip          show rip routing table
ospf         show ospf routing table
bgp          show bgp routing table
isis         show isis routing table
static       show static routing table
connected    show connected routing table
database     show routing information base
```
Con este comando nos lista los puertos conectados 
SITE-A # get router info routing-table connected 
```
Routing table for VRF=0
C       10.0.1.0/24 is directly connected, port4
C       172.21.0.0/30 is directly connected, port7
C       180.45.22.0/30 is directly connected, port3
C       192.168.1.0/24 is directly connected, port1
C       200.212.31.0/30 is directly connected, port2
```
Aquí solo vemos las estaticas
```
SITE-A # get router info routing-table static
Routing table for VRF=0
S*      0.0.0.0/0 [10/0] via 180.45.22.2, port3
S       10.0.2.0/24 [10/0] via 172.21.0.2, port7
```
Nos muestra casi todos excepto el puerto 3 que a pesar de estar conectado no lo muestra por el salto.

```
Con este comando no nos esta mostrado el puerto a pesar de estar conectado no esta siendo utilizada para la transferencia de datos.
SITE-A # get router info routing-table all
Codes: K - kernel, C - connected, S - static, R - RIP, B - BGP
       O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default

Routing table for VRF=0
S*      0.0.0.0/0 [10/0] via 180.45.22.2, port3
C       10.0.1.0/24 is directly connected, port4
S       10.0.2.0/24 [10/0] via 172.21.0.2, port7
C       172.21.0.0/30 is directly connected, port7
C       180.45.22.0/30 is directly connected, port3
C       192.168.1.0/24 is directly connected, port1
C       200.212.31.0/30 is directly connected, port2
```
Con este comando si nos muestra el puerto 3 a pesar de que la transferencia de datos aparece como 0.0.0.0/0 que indica que esta activa pero sin uso para la transferencia de datos.
```
SITE-A # get router info routing-table database 
Codes: K - kernel, C - connected, S - static, R - RIP, B - BGP
       O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       > - selected route, * - FIB route, p - stale info

Routing table for VRF=0
S    *> 0.0.0.0/0 [10/0] via 180.45.22.2, port3
S       0.0.0.0/0 [15/0] via 200.212.31.2, port2
C    *> 10.0.1.0/24 is directly connected, port4
S    *> 10.0.2.0/24 [10/0] via 172.21.0.2, port7
C    *> 172.21.0.0/30 is directly connected, port7
C    *> 180.45.22.0/30 is directly connected, port3
C    *> 192.168.1.0/24 is directly connected, port1
C    *> 200.212.31.0/30 is directly connected, port2
```
Porque si el puerto estuviera realmente inactivo en la parte de adelante como este ejemplo el puerto 2 esta inactivo debido a que lo puse como estatus disable.
*> la falta de flechita indica que esta inactivo por la falta de transferencia de datos, no porqué esté desactivado.

```
SITE-A # get router info routing-table database
Codes: K - kernel, C - connected, S - static, R - RIP, B - BGP
       O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       > - selected route, * - FIB route, p - stale info

Routing table for VRF=0
S    *> 0.0.0.0/0 [10/0] via 180.45.22.2, port3
S       0.0.0.0/0 [15/0] via 200.212.31.2, port2 inactive
C    *> 10.0.1.0/24 is directly connected, port4
S    *> 10.0.2.0/24 [10/0] via 172.21.0.2, port7
C    *> 172.21.0.0/30 is directly connected, port7
C    *> 180.45.22.0/30 is directly connected, port3
C    *> 192.168.1.0/24 is directly connected, port1
```
Ahora para ver la policy route es con el siguinete comando

```
SITE-B # diagnose firewall proute list
list route policy info(vf=root):

id=1 dscp_tag=0xff 0xff flags=0x0 tos=0x00 tos_mask=0x00 protocol=6 sport=0-65535 iif=6 dport=80 path(1) oif=4(port2) gwy=69.89.123.2 
source wildcard(1): 10.0.2.10/255.255.255.255 
destination wildcard(1): 10.0.1.10/255.255.255.255 
hit_count=0 last_used=2022-10-19 15:19:15
```
## Atributos de las rutas

¿Qué pasa si los ISP tiene la misma distancia?
En este caso 15 para el ISP1 y ISP2 entonces podemos modificar la opción prioridad.

Para este ejemplo ingresamos en Netowor->Static Routes->
Modificar las ISP's con 15 

![65](66.png)

![66](67.png)
Aquí modificamos la prioridad en este ejemplo 3
![67](68.png)

Habilitamos la casilla Prioridad, en este caso la priopridad se da quien tenga el número más pequeño en este caso es 0 (ISP2,Puerto3)

![68](69.png)

Vamos a crear un firewall policy con el nombre INTERNET-ISP2(Policy & Objects->Firewall policy->Create New)

![69](70.png)

Una vez creado hacemos un ping desde la PC2 -10.0.2.10 hacia google y revisamos porque puerto salimos, en la imagen se muestran el antes(arriba) y después(abajo) en este último se muestra que tiene actividad en kb (señalado con la flecha roja)
![70](71.png)

¿Y porqué pasa esto? Sí tiene que ver con con la secuencia en este caso la prioridad cae en el ISP2 porqué está en 0, mientras el ISP1 tiene la prioridad de 3.

![71](72.png)

Como sería con CLI

Se interpreta de la siguiente manera
```
SITE-B # get router info routing-table database 
Codes: K - kernel, C - connected, S - static, R - RIP, B - BGP
       O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       > - selected route, * - FIB route, p - stale info
```
Aquí se puede observar que el puerto 3 no tiene nada enfrente debido a que las configuraciones por default son cero y no se muestran.

El puerto 2 nos muestra enfren [3/0] ya que la prioridad es 3 y la metrica 0
```
Routing table for VRF=0
S    *> 0.0.0.0/0 [15/0] via 45.32.12.2, port3
     *>           [15/0] via 60.89.123.2, port2, [3/0]
S    *> 10.0.1.0/24 [10/0] via 172.21.0.1, port7
C    *> 10.0.2.0/24 is directly connected, port4
C    *> 45.32.12.0/30 is directly connected, port3
C    *> 60.89.123.0/30 is directly connected, port2
C    *> 172.21.0.0/30 is directly connected, port7
C    *> 192.168.1.0/24 is directly connected, port1
```
## Routing para servicios de Internet

Es solo teoríco debido a que por el tipo de licencia no se puede realizar.(revisar curso)

Ir a Network->Static Route
En este ejemplo creamos una ruta estatica para salir a través del puerto 3 y hacer una consulta a los servidores DNS de google.
![72](73.png)

## Ruta de acceso múltiple de igual coste (ECMP)

Balanceo de Carga

Lo primero que se debe de hacer es que la Distancia Administrativa en ambos ISP sea la misma en este caso 10 (Network->Static Routes-> ISP 1 y 2 ->administrativa Distance 10) así como las Prioridades deben de ser iguales en este caso 5.

Con estas configuraciones ya tendriamos nuestro ICMP funcionando (balanceo de carga)
![73](74.png)

Si hacemos la consulta con el CLI vemos lo siguiente:

```
SITE-A # get rou info routing-table database
Codes: K - kernel, C - connected, S - static, R - RIP, B - BGP
       O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       > - selected route, * - FIB route, p - stale info

Routing table for VRF=0
S    *> 0.0.0.0/0 [10/0] via 180.45.22.2, port3, [5/0]
     *>           [10/0] via 200.212.31.2, port2, [5/0]
C    *> 10.0.1.0/24 is directly connected, port4
S    *> 10.0.2.0/24 [10/0] via 172.21.0.2, port7
C    *> 172.21.0.0/30 is directly connected, port7
C    *> 180.45.22.0/30 is directly connected, port3
C    *> 192.168.1.0/24 is directly connected, port1
C    *> 200.212.31.0/30 is directly connected, port2

```
Si hacemos un ping podremos ver que sigue saliendo solo por el ISP, y no ambos (mayor o menor proporción de bytes de transferencia)

![74](75.png)

La manera de trabajar de ECMP tiene diferntes modos y solo se puede ver a través de la CLI.

Con este comando podemos revisar de que manera está trabajando el ECMP actualmente.

Y lo que indica el Source-ip-based, es que hace una especie de sorte por ejemplo al querer navegar a través de google.com siempre va a salir de ese puerto, si ponemos yahoo.com y de nuevo escoge ese puerto de nuevo va salir por el ISP1, ahora si navegamos en altavista.com y sale por el ISP2 siempre va salir por ese puerto desde ese momento y no se va a modificar.

```
SITE-B (settings) # get | grep ecmp
v4-ecmp-mode        : source-ip-based 
ecmp-max-paths      : 255
``` 

Con este comando podemos ver las opciones en la que puede trabajar el ECMP

```
SITE-B (settings) # set v4-ecmp-mode 
source-ip-based         Select next hop based on source IP.
weight-based            Select next hop based on weight.
usage-based             Select next hop based on usage.
source-dest-ip-based    Select next hop based on both source and destination IPs.
```

Lo que nosotros queremos es que el "sorte" se haga en cualquier ISP, por ejemplo si ponemos google.com salga en este momento por ISP1 y al volver a navegar ahora va salir por ISP2.

Para hacer esto necesitamos del source-dest-ip-based
```
SITE-B # conf systems settings
SITE-B (settings) # set v4-ecmp-mode source-dest-ip-based 

SITE-B (settings) # end
```
Ahora si hacemos una prueba con ping a google.com, yahoo.com, apple.com

Nos va a mostrar lo siguiente
![75](76.png)

Y si nos vamos a Dashboard->FortiView Sessión veremos que toma distintos puertos para la salida
![76](77.png)

## Healthy Link Monitor

Sirven para programar monitoreo sobre los enlaces, esto quiere decir algún enlace SP1 o SP2 tiene alguna falla por ejemplo que el cable se rompa, podemos utilizar el otro servicio disponible o backup.

Para hacer esta prueba vamos a ingresar a nuestro mikrotik 

Una vez dentro presionamos enter y luego /ip addres, después print para saber los valores de los puertos.
y cambios la IP funcional
```
set address = 200.212.31.2/30 numbers=0
```
Por esta.
```
set address = 200.212.30.2/30 numbers=0
```
![77](78.png)

Despues realizamos un ping a google y no inidicará que no hace ping, y es aquí dónde preguntamos ¿porqué no hace el cambio automático?
```
SITE-A # exec ping google.com
Unable to resolve hostname.
```
Enonces lo que tenemos que hacer son unos cambios a través del CLI(única opción) ingresmaos con los siguientes comandos

```
SITE-A # conf syst link-monitor 

SITE-A (link-monitor) # show
config system link-monitor
end

SITE-A (link-monitor) # edit 1
new entry '1' added

SITE-A (1) # get 
name                : 1
addr-mode           : ipv4 
srcintf             : 
server-config       : default 
server              :
protocol            : ping 
gateway-ip          : 0.0.0.0
route               :
source-ip           : 0.0.0.0
interval            : 500
probe-timeout       : 500
failtime            : 5
recoverytime        : 5
probe-count         : 30
ha-priority         : 1
update-cascade-interface: enable 
update-static-route : enable 
update-policy-route : enable 
status              : enable 
diffservcode        : 000000
class-id            : 0
service-detection   : disable 
         

SITE-A (1) # set protocol 
ping        PING link monitor.
tcp-echo    TCP echo link monitor.
udp-echo    UDP echo link monitor.
http        HTTP-GET link monitor.
twamp       TWAMP link monitor.
 
SITE-A (1) # set srcintf port2

SITE-A (1) # set server 8.8.8.8

SITE-A (1) # set gateway-ip 200.212.31.2

SITE-A (1) # show
config system link-monitor
    edit "1"
        set srcintf "port2"
        set server "8.8.8.8"
        set gateway-ip 200.212.31.2
    next
end

SITE-A (1) # end
```
Después volvemos hacer ping y ya recibimos respuesta y revisamos con el CLI y podremos ver que le puerto 2 esta inactivo, y ahora el que está en uso es el puerto 3.

```
SITE-A # get router  info routing-table database 
Codes: K - kernel, C - connected, S - static, R - RIP, B - BGP
       O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       > - selected route, * - FIB route, p - stale info

Routing table for VRF=0
S    *> 0.0.0.0/0 [15/0] via 180.45.22.2, port3, [5/0]
S       0.0.0.0/0 [10/0] via 200.212.31.2, port2 inactive, [5/0]
C    *> 10.0.1.0/24 is directly connected, port4
C    *> 180.45.22.0/30 is directly connected, port3
C    *> 192.168.1.0/24 is directly connected, port1
C    *> 200.212.31.0/30 is directly connected, port2


SITE-A # 
```

## Packet Sniffer por CLI
```
SITE-A # diagnose sniffer packet port4 icmp
Using Original Sniffing Mode
interfaces=[port4]
filters=[icmp]
14.061164 10.0.1.10 -> 8.8.8.8: icmp: echo request
14.072057 8.8.8.8 -> 10.0.1.10: icmp: echo reply
15.082424 10.0.1.10 -> 8.8.8.8: icmp: echo request
15.094323 8.8.8.8 -> 10.0.1.10: icmp: echo reply
16.121377 10.0.1.10 -> 8.8.8.8: icmp: echo request
16.131998 8.8.8.8 -> 10.0.1.10: icmp: echo reply
17.136812 10.0.1.10 -> 8.8.8.8: icmp: echo request
17.148429 8.8.8.8 -> 10.0.1.10: icmp: echo reply
38.363189 10.0.1.10 -> 8.8.8.8: icmp: echo request
38.374271 8.8.8.8 -> 10.0.1.10: icmp: echo reply
39.390959 10.0.1.10 -> 8.8.8.8: icmp: echo request
39.401555 8.8.8.8 -> 10.0.1.10: icmp: echo reply
40.409235 10.0.1.10 -> 8.8.8.8: icmp: echo request
40.424508 8.8.8.8 -> 10.0.1.10: icmp: echo reply
41.422689 10.0.1.10 -> 8.8.8.8: icmp: echo request
41.435227 8.8.8.8 -> 10.0.1.10: icmp: echo reply
236.223140 10.0.1.10 -> 1.1.1.1: icmp: 10.0.1.10 udp port 60614 unreachable
```

Ahora cuando hacemos un ping como prueba en el CLI se ve que está apuntado hacía ping google.com
![78](79.png)

Pero si voy a google.com a través del navegador nos indica que es inalcanzable

```
365.857297 10.0.1.10 -> 1.1.1.1: icmp: 10.0.1.10 udp port 59754 unreachable
```
Podemos agregar más cosas a nuestro sniffer, en este caso solo esta analizando la computadora con la ip 10.0.1.10
```
SITE-A # diagnose sniffer packet port4 "icmp && src host 10.0.1.10"
Using Original Sniffing Mode
interfaces=[port4]
filters=[icmp && src host 10.0.1.10]
16.619767 10.0.1.10 -> 172.217.15.14: icmp: echo request
17.639169 10.0.1.10 -> 172.217.15.14: icmp: echo request
18.675029 10.0.1.10 -> 172.217.15.14: icmp: echo request
19.724220 10.0.1.10 -> 172.217.15.14: icmp: echo request
^C
4 packets received by filter
0 packets dropped by kernel

SITE-A # diagnose sniffer packet port4 "icmp && dst host 10.0.1.10"
Using Original Sniffing Mode
interfaces=[port4]
filters=[icmp && dst host 10.0.1.10]
3.822961 172.217.15.14 -> 10.0.1.10: icmp: echo reply
4.843801 172.217.15.14 -> 10.0.1.10: icmp: echo reply
5.886665 172.217.15.14 -> 10.0.1.10: icmp: echo reply
6.919011 172.217.15.14 -> 10.0.1.10: icmp: echo reply
```
Podemos revisar también los puertos UDP y el puerto especificado en este caso 53

```
SITE-A # diagnose sniffer packet port4 "udp && port 53 && host 10.0.1.10"
Using Original Sniffing Mode
interfaces=[port4]
filters=[udp && port 53 && host 10.0.1.10]
0.911310 8.8.8.8.53 -> 10.0.1.10.60440: udp 110
0.919013 8.8.8.8.53 -> 10.0.1.10.63251: udp 37
0.921444 10.0.1.10.50324 -> 8.8.8.8.53: udp 35
1.008526 10.0.1.10.50324 -> 1.1.1.1.53: udp 35
1.017433 8.8.8.8.53 -> 10.0.1.10.50324: udp 35
1.030162 10.0.1.10.62335 -> 8.8.8.8.53: udp 33
1.069049 1.1.1.1.53 -> 10.0.1.10.50324: udp 35
1.083327 8.8.8.8.53 -> 10.0.1.10.62335: udp 33
1.085224 10.0.1.10.49216 -> 8.8.8.8.53: udp 30
1.089580 8.8.8.8.53 -> 10.0.1.10.49216: udp 98
1.134984 10.0.1.10.64369 -> 8.8.8.8.53: udp 30
1.188834 8.8.8.8.53 -> 10.0.1.10.64369: udp 105
1.193086 10.0.1.10.64235 -> 8.8.8.8.53: udp 40
1.261943 8.8.8.8.53 -> 10.0.1.10.64235: udp 115
1.265766 10.0.1.10.59754 -> 8.8.8.8.53: udp 45
1.324262 8.8.8.8.53 -> 10.0.1.10.59754: udp 120
1.328335 10.0.1.10.61434 -> 8.8.8.8.53: udp 45
1.380075 8.8.8.8.53 -> 10.0.1.10.61434: udp 120
39.089174 10.0.1.10.59754 -> 8.8.8.8.53: udp 27
39.145392 8.8.8.8.53 -> 10.0.1.10.59754: udp 43
^C
20 packets received by filter
0 packets dropped by kernel

SITE-A #

Nota: Revisar el documento de verbosidad.
```
## SD-WAN (Software Define WAN - Acceso a internet definido por software)

What is SD-WAN
 *Virtual interface consisting of a group of member interfaces that can be connected to different link types
 *Allows affective WAN usage with various load balancing algorithms
 *Supports link Quality measurement
   *Dynamic link selection based on link quality
   *Ensurance high availibility of business-critical applications

SDWAN lo encontramos en Network->SD-WAN
Vamos a encontrar tres opciones 
- SD-WAN Zones
- SD-WAN Rules
- SD-WAN SLAs

En este ejemplo vamos a ocupar SD-WAN Zones ahora vamos a dar click en CREATE NEW y encontramos otras dos opciones 
- SD-WAN Member 
- SD-WAN Zone

Seleccionamos SD-WAN Zone y ingreamos el nombre SDWAN-LAN y click en ok

![80](81.png)

En la opción de Interface members click en plus (+) y en la opción seleccionar entrada click en CREATE  y rellenamos los datos solicitados


```
INTERFACE:ISP1(Port2)
SD-WAN Zone:SDWAN-LAN
Gateway:200.212.31.12

Click en ok
```
![81](82.png)

```
INTERFACE:ISP2(Port3)
SD-WAN Zone:SDWAN-LAN
Gateway:180.45.22.2

Click en ok
```
![82](83.png)

Ahora se tienen que crear una ruta estatica y ponemos los datos solicitados
```
Subnet:0.0.0.0/0.0.0.0
Interface:SD-WAN-LAN 
Estatus:Enabled

Click en ok
```
![83](84.png)

Ahora vamos a crear una nueva firewall policy

```
Name:INTERNET-SDWAN
Incoming:LAN4
outgoing:SDWAN-LAN
Source:all
Destination:all
Schedule:Always
service:all
NAT:Enable

Click en ok
```
![84](85.png)

ahora probamos en el servidor el internet con un ping y con el navegador y verificamos que funciona.

## SD-WAN Performance SLAs
Son testigos para probar nuestos ISP's esten funcionando correctamente y no esten caidos, ya hay algunas opciones como la de amazon.
con

## SD-WAN Rules

SD-WAN->SD-WAN Rules->crear nuevo
![87](88.png)

Nos mostrará cuál es la mejor opción para el transito de datos por algún puerto.
![88](89.png)

## VDOMS

![89](90.png)

![90](91.png)

![91](92.png)

![92](93.png)

![93](94.png)

Lamentablemente en el Laboratorio no se puede implentar así que el profe lo hace directamente en su equipo físco y serán puras capturas de pantalla.

Para crear los VDOM's se realiza de manera gráfica.
System->Settings
sin embargo con ese equipo físco no permite hacerlo de manera gráfica
![94](95.png)

Por lo tanto se realizará de manera de consola.
>config system global
(global)set vdom-mode ?

![95](96.png)


Ahora en la parte susperio podremos ver la opción de VDOM

![96](97.png)

## VLANS

![97](98.png)

En este laboratorio se van agregar Vlans 

![98](99.png)

Y esta es la configuración de cada  SW cisco

```
#CONFIGURACION SWITCH A

enable
configure terminal
vlan 101
name Legales
exit
vlan 102
name Contable
exit
interface gigabitEthernet 0/1
switchport mode access
switchport access vlan 101
exit
interface gigabitEthernet 0/2
switchport mode access
switchport access vlan 102
exit
interface gigabitEthernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk
end



#CONFIGURACION SWITCH B

enable
configure terminal
vlan 103
name Sistemas
exit
interface gigabitEthernet 0/1
switchport mode access
switchport access vlan 103
exit
interface gigabitEthernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk
end
```

Una vez configurada las vlans en los Sw's ahora vamos a configurar en el equipo FG

Network->Interfaces->Create new Interfaces (x3)
```
1)
Name:Legales
Type:Vlan
Vlan Protocol:802Q
Port2
Vlan Id:101
Role:Lan
IP:172.21.0.254/24
habilitamos ping

2)
Name:Contable
Type:Vlan
802Q
Port2
Vlan Id:102
Role:Lan
IP:172.22.0.254/24
habilitamos ping

3)
Name:Sistemas
Type:Vlan
802Q
Port3
Vlan Id:103
Role:Lan
IP:172.23.0.254/24
habilitamos ping
```

![99](100.png)


Ahora nos vamos a Policy & Objects -> Firewall policy->Create New

```
Name:Lagales->Sistemas
Icoming:Legales
Outgoing:Sistemas
Source:all
Destination.all
Nat:Disable

Ahora vamos con botón derecho aplicamos en Clone reverso y renombrar 

Sistemas->Legales
```

![100](101.png)

Ahora si hacemos un ping obtendremos respuesta:
```
PC-LAN-102> ping 172.22.0.254

84 bytes from 172.22.0.254 icmp_seq=1 ttl=255 time=3.340 ms
84 bytes from 172.22.0.254 icmp_seq=2 ttl=255 time=9.338 ms
84 bytes from 172.22.0.254 icmp_seq=3 ttl=255 time=4.445 ms
84 bytes from 172.22.0.254 icmp_seq=4 ttl=255 time=4.386 ms
84 bytes from 172.22.0.254 icmp_seq=5 ttl=255 time=3.250 ms

PC-LAN-102> ping 172.22.0.1

172.22.0.1 icmp_seq=1 ttl=64 time=0.001 ms
172.22.0.1 icmp_seq=2 ttl=64 time=0.001 ms
172.22.0.1 icmp_seq=3 ttl=64 time=0.001 ms
172.22.0.1 icmp_seq=4 ttl=64 time=0.001 ms
172.22.0.1 icmp_seq=5 ttl=64 time=0.001 ms
```

## Virtual Switch 

![101](102.png)

![102](103.png)

Aparte de Software Switch es diferente a Hardware Sw debido a que este último requiere un dispositivo para conectarse necesita un chip dedicado.

Para crear un Software Sw (SSW) necesitamos ingresar a 
```
Network->Interface->Create New Interface

Name:Software_Switch
Type:Software Switch
Interface member:Port7 Port8
Role:LAN

Address
manual
IP/Netmas:10.54.0.1/24
Activar Dhcp

```

![103](104.png)

Para probar agregamos dos equpos en GNS3 tipo VPCS
y conectamos uno a puerto 7 y otro a 8
para que tome el dhcp presionamos dhcp
revisamos la ip asiganda y hacemos ping

```
PC2> dhcp
DORA IP 10.54.0.2/24 GW 10.54.0.1

PC2>
PC2>



PC1> dhcp
DORA IP 10.54.0.3/24 GW 10.54.0.1

PC1>

PC1> ping 10.54.0.2

84 bytes from 10.54.0.2 icmp_seq=1 ttl=64 time=0.940 ms
84 bytes from 10.54.0.2 icmp_seq=2 ttl=64 time=3.717 ms
84 bytes from 10.54.0.2 icmp_seq=3 ttl=64 time=2.677 ms
84 bytes from 10.54.0.2 icmp_seq=4 ttl=64 time=3.500 ms
84 bytes from 10.54.0.2 icmp_seq=5 ttl=64 time=3.639 ms

PC1>


PC2> ping 10.54.0.3

84 bytes from 10.54.0.3 icmp_seq=1 ttl=64 time=0.797 ms
84 bytes from 10.54.0.3 icmp_seq=2 ttl=64 time=2.205 ms
84 bytes from 10.54.0.3 icmp_seq=3 ttl=64 time=1.333 ms
84 bytes from 10.54.0.3 icmp_seq=4 ttl=64 time=1.367 ms
84 bytes from 10.54.0.3 icmp_seq=5 ttl=64 time=2.355 ms
```
## Modo de operación
### El modo NAT es que hemos utilizado hasta el momento en el curso.

### Modo Transparente
Solo se utiliza para hacer uso de las funcionalidades de seguridad

![104](105.png)

```
SITE-B # execute factoryreset
This operation will reset the system to factory default!
Do you want to continue? (y/n)y


System is resetting to factory default...


The system is going down NOW !!

SITE-B #
Please stand by while rebooting the system.
Restarting system.

System is starting...
Serial number is FGVMEV3CAKWZQA1A


FortiGate-VM64-KVM login: admin
Password:
Login incorrect


FortiGate-VM64-KVM login: admin
Password:
You are forced to change your password. Please input a new password.
New Password:
Confirm Password:
Welcome!

WARNING: File System Check Recommended! An unsafe reboot may have caused an inconsistency in the disk drive.
It is strongly recommended that you check the file system consistency before proceeding.
Please run 'execute disk list' and then 'execute disk scan <ref#>'.
Note: The device will reboot and scan the disk during startup. This may take up to an hour.
FortiGate-VM64-KVM # config system interface

FortiGate-VM64-KVM (interface) # edit port1

FortiGate-VM64-KVM (port1) # append allowaccess http

FortiGate-VM64-KVM (port1) # end

FortiGate-VM64-KVM # conf system settings

FortiGate-VM64-KVM (settings) # set opmode

incomplete command in the end
Command fail. Return code -160

FortiGate-VM64-KVM (settings) # set opmode transparent

FortiGate-VM64-KVM (settings) # emd
Unknown action 0

FortiGate-VM64-KVM (settings) # end
Cannot change to Transparent mode because this vdom contains managed switches and switchctl-vlans.
Please clear managed-switches, disable fortilink and retry.
node_check_object fail! for opmode transparent
Attribute 'opmode' value 'transparent' checking fail -7610
Command fail. Return code -7610

FortiGate-VM64-KVM # conf system  interface

FortiGate-VM64-KVM (interface) # show
config system interface
    edit "port1"
        set vdom "root"
        set mode dhcp
        set allowaccess ping https ssh http fgfm
        set type physical
        set snmp-index 1
    next
    edit "port2"
        set vdom "root"
        set type physical
        set snmp-index 2
    next
    edit "port3"
        set vdom "root"
        set type physical
        set snmp-index 3
    next
    edit "port4"
        set vdom "root"
        set type physical
        set snmp-index 4
    next
    edit "port5"
        set vdom "root"
        set type physical
        set snmp-index 5
    next
    edit "port6"
        set vdom "root"
        set type physical
        set snmp-index 6
    next
    edit "port7"
        set vdom "root"
        set type physical
        set snmp-index 7
    next
    edit "port8"
        set vdom "root"
        set type physical
        set snmp-index 8
    next
    edit "port9"
        set vdom "root"
        set type physical
        set snmp-index 9
    next
    edit "port10"
        set vdom "root"
        set type physical
        set snmp-index 10
    next
    edit "naf.root"
        set vdom "root"
        set type tunnel
        set src-check disable
        set snmp-index 11
    next
    edit "l2t.root"
        set vdom "root"
        set type tunnel
        set snmp-index 12
    next
    edit "ssl.root"
        set vdom "root"
        set type tunnel
        set alias "SSL VPN interface"
        set snmp-index 13
    next
    edit "fortilink"
        set vdom "root"
        set fortilink enable
        set ip 10.255.1.1 255.255.255.0
        set allowaccess ping fabric
        set type aggregate
        set lldp-reception enable
        set lldp-transmission enable
        set snmp-index 14
    next
end

FortiGate-VM64-KVM (interface) # delete fortilink
The entry is used by other 2 entries
Command fail. Return code -23

FortiGate-VM64-KVM (interface) # end

FortiGate-VM64-KVM # show | grep -fi fort
command parse error before '|'

FortiGate-VM64-KVM # show | grep -fi forti
command parse error before '|'

FortiGate-VM64-KVM # show | grep -fi fortilink
config system interface
    edit "fortilink" <---
        set vdom "root"
        set fortilink enable <---
        set ip 10.255.1.1 255.255.255.0
        set allowaccess ping fabric
        set type aggregate
        set lldp-reception enable
        set lldp-transmission enable
        set snmp-index 14
    next
end
config system ntp
    set ntpsync enable
    set server-mode enable
    set interface "fortilink" <---
end
config system dhcp server
    edit 1
        set ntp-service local
        set default-gateway 10.255.1.1
        set netmask 255.255.255.0
        set interface "fortilink" <---
        set vci-match enable
        set vci-string "FortiSwitch" "FortiExtender"
    next
end
config switch-controller storm-control-policy
    edit "auto-config"
        set description "storm control policy for fortilink-isl-icl port" <---
        set storm-control-mode disabled
    next
end

FortiGate-VM64-KVM # confi system ntp

FortiGate-VM64-KVM (ntp) # set ntpsync disable

FortiGate-VM64-KVM (ntp) # set server-mode disable

FortiGate-VM64-KVM (ntp) # show
config system ntp
end

FortiGate-VM64-KVM (ntp) # end

FortiGate-VM64-KVM # confi system dhcp server

FortiGate-VM64-KVM (server) # show
config system dhcp server
    edit 1
        set ntp-service local
        set default-gateway 10.255.1.1
        set netmask 255.255.255.0
        set interface "fortilink"
        config ip-range
            edit 1
                set start-ip 10.255.1.2
                set end-ip 10.255.1.254
            next
        end
        set vci-match enable
        set vci-string "FortiSwitch" "FortiExtender"
    next
end

FortiGate-VM64-KVM (server) # delte 1
Unknown action 0

FortiGate-VM64-KVM (server) # delete 1

FortiGate-VM64-KVM (server) # show
config system dhcp server
end

FortiGate-VM64-KVM (server) # end

FortiGate-VM64-KVM # Timeout


FortiGate-VM64-KVM login: admin
Password:
Welcome!

WARNING: File System Check Recommended! An unsafe reboot may have caused an inconsistency in the disk drive.
It is strongly recommended that you check the file system consistency before proceeding.
Please run 'execute disk list' and then 'execute disk scan <ref#>'.
Note: The device will reboot and scan the disk during startup. This may take up to an hour.
FortiGate-VM64-KVM # get system interface
== [ port1 ]
name: port1   mode: dhcp    ip: 192.168.1.174 255.255.255.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port2 ]
name: port2   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port3 ]
name: port3   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port4 ]
name: port4   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port5 ]
name: port5   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port6 ]
name: port6   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port7 ]
name: port7   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port8 ]
name: port8   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port9 ]
name: port9   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ port10 ]
name: port10   mode: static    ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: physical   ring-rx: 0   ring-tx: 0   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable
== [ naf.root ]
name: naf.root   ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: tunnel   netflow-sampler: disable    sflow-sampler: disable    src-check: disable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    wccp: disable
== [ l2t.root ]
name: l2t.root   ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: tunnel   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    wccp: disable
== [ ssl.root ]
name: ssl.root   ip: 0.0.0.0 0.0.0.0   status: up    netbios-forward: disable    type: tunnel   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    wccp: disable
== [ fortilink ]
name: fortilink   mode: static    ip: 10.255.1.1 255.255.255.0   status: up    netbios-forward: disable    type: aggregate   netflow-sampler: disable    sflow-sampler: disable    src-check: enable    explicit-web-proxy: disable    explicit-ftp-proxy: disable    proxy-captive-portal: disable    mtu-override: disable    wccp: disable    drop-overlapped-fragment: disable    drop-fragment: disable


FortiGate-VM64-KVM # conf system  settings

FortiGate-VM64-KVM (settings) # set opmode transparent

FortiGate-VM64-KVM (settings) # end
Cannot change to Transparent mode because this vdom contains managed switches and switchctl-vlans.
Please clear managed-switches, disable fortilink and retry.
node_check_object fail! for opmode transparent
Attribute 'opmode' value 'transparent' checking fail -7610
Command fail. Return code -7610

FortiGate-VM64-KVM # show | grep -fi fortilink
config system interface
    edit "fortilink" <---
        set vdom "root"
        set fortilink enable <---
        set ip 10.255.1.1 255.255.255.0
        set allowaccess ping fabric
        set type aggregate
        set lldp-reception enable
        set lldp-transmission enable
        set snmp-index 14
    next
end
config switch-controller storm-control-policy
    edit "auto-config"
        set description "storm control policy for fortilink-isl-icl port" <---
        set storm-control-mode disabled
    next
end

FortiGate-VM64-KVM # show | grep -fi fortilink
config system interface
    edit "fortilink" <---
        set vdom "root"
        set fortilink enable <---
        set ip 10.255.1.1 255.255.255.0
        set allowaccess ping fabric
        set type aggregate
        set lldp-reception enable
        set lldp-transmission enable
        set snmp-index 14
    next
end
config switch-controller storm-control-policy
    edit "auto-config"
        set description "storm control policy for fortilink-isl-icl port" <---
        set storm-control-mode disabled
    next
end

FortiGate-VM64-KVM # delete for
command parse error before 'delete'

FortiGate-VM64-KVM # delete fortilink
Unknown action 0

FortiGate-VM64-KVM # show | grep -fi fortilink
config system interface
    edit "fortilink" <---
        set vdom "root"
        set fortilink enable <---
        set ip 10.255.1.1 255.255.255.0
        set allowaccess ping fabric
        set type aggregate
        set lldp-reception enable
        set lldp-transmission enable
        set snmp-index 14
    next
end
config switch-controller storm-control-policy
    edit "auto-config"
        set description "storm control policy for fortilink-isl-icl port" <---
        set storm-control-mode disabled
    next
end

FortiGate-VM64-KVM # conf sys interface

FortiGate-VM64-KVM (interface) # delete fortilink

FortiGate-VM64-KVM (interface) # end

FortiGate-VM64-KVM # show | grep -fi fortilink
config switch-controller storm-control-policy
    edit "auto-config"
        set description "storm control policy for fortilink-isl-icl port" <---
        set storm-control-mode disabled
    next
end

FortiGate-VM64-KVM # set opmode transparent
Unknown action 0


FortiGate-VM64-KVM # config system settings

FortiGate-VM64-KVM (settings) # set opmode transparent

FortiGate-VM64-KVM (settings) # set manageip 192.168.1.174/24

FortiGate-VM64-KVM (settings) # set gateway 192.168.1.254

FortiGate-VM64-KVM (settings) # end

Changing to TP mode

```
```
FortiGate-VM64-KVM # get system status
Version: FortiGate-VM64-KVM v7.0.3,build0237,211207 (GA)
Virus-DB: 1.00000(2018-04-09 18:07)
Extended DB: 1.00000(2018-04-09 18:07)
Extreme DB: 1.00000(2018-04-09 18:07)
AV AI/ML Model: 0.00000(2001-01-01 00:00)
IPS-DB: 6.00741(2015-12-01 02:30)
IPS-ETDB: 6.00741(2015-12-01 02:30)
APP-DB: 6.00741(2015-12-01 02:30)
INDUSTRIAL-DB: 6.00741(2015-12-01 02:30)
IPS Malicious URL Database: 1.00001(2015-01-01 01:01)
Serial-Number: FGVMEV3CAKWZQA1A
License Status: Valid
Evaluation License Expires: Fri Jan 27 08:04:17 2023
VM Resources: 1 CPU/1 allowed, 997 MB RAM/2048 MB allowed
Log hard disk: Available
Hostname: FortiGate-VM64-KVM
Operation Mode: Transparent
Current virtual domain: root
Max number of virtual domains: 1
Virtual domains status: 0 in NAT mode, 1 in TP mode
Virtual domain configuration: disable
FIPS-CC mode: disable
Current HA mode: standalone
Branch point: 0237
Release Version Information: GA
```
![106](107.png)

Ahora en el laboratorio haremos la prueba de salida a internet y para eso tenemos que activar una nueva política.

```
Firewall->Policy&Objects->Firewall policy
Name:LAN-INTERNET
Incoming:Port2
Outgoing:Port3
Source:all
Destination:all
service:all
Al estar en modo transparente (capa2) no se puede habilitar el NAT.
```
![107](108.png)
Si hacemos un ping tendremos respuesta.
![108](109.png)
En este laboratorio se puso un FG entre el router y la pc, su función es que este modo firewall (capa2)
![109](110.png)

### Modo Trasnparente -> Virtual Wire Pair
Está configuración también se puede configurar con el NAT
```
Network->Interface->Create New->Virtual Wire Pair
Name:p2-p3
Interface Name:Port2 Port3

Al hacer esto en Policy & objects nos aparece una opción llamada Firewall Virtual Wire pair policy

```
![110](111.png)
```
Click en irewall Virtual Wire pair policy->New policy
Y lo nuevo que nos encontramos es la dirección de port2 -> port3, puede ser de viceversa o bidireccional
```
![111](112.png)

Lo que se crea es un circuito, el tráfico que entre por alguna (port2<->) de esas interfaces, solamente va poder ir hacia la otra, pierde conexión con las demas interfaces(port4,port5,port6..portn) tiene una ventaja ya que reduce al broadcast.

## Alta Disponibilidad (HA)
![112](113.png)

Existen dos modos
El primero es Activo-Pasivo:
![113](114.png)
El siguiente es Activo-Activo:
![114](115.png)
Esto es posible gracias al protoco FGCP
![115](116.png)

Requerimientos, nota importante es necesario que todos los equipos tengan el mismo tipo de licenciamiento, esto debido a que si alguno de los equipos cuenta con un licenciamiento inferior todo el cluster va a correr o comportarse de la misma manera al inferior. 
También el cluster tiene que tener la misma configuración de cableado en todos los equipos.
![116](117.png)

Existen elementos que no son syncronizados entre los FG
![117](118.png)

```
FortiGate-VM64-KVM # conf system interface

FortiGate-VM64-KVM (interface) # edit port2

FortiGate-VM64-KVM (port2) # set ip 45.32.12.1/30

FortiGate-VM64-KVM (port2) # end

hacemos un ping en dónde se encuentra el respaldo 
FG-HA-1 # exec ping 45.32.12.2
PING 45.32.12.2 (45.32.12.2): 56 data bytes
64 bytes from 45.32.12.2: icmp_seq=0 ttl=64 time=2.0 ms
64 bytes from 45.32.12.2: icmp_seq=1 ttl=64 time=1.3 ms
64 bytes from 45.32.12.2: icmp_seq=2 ttl=64 time=1.0 ms
64 bytes from 45.32.12.2: icmp_seq=3 ttl=64 time=3.7 ms
64 bytes from 45.32.12.2: icmp_seq=4 ttl=64 time=3.2 ms

--- 45.32.12.2 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 1.0/2.2/3.7 ms

Ejecutamos la restauración 

FG-HA-1 # exec restore config ftp HA-1-READY 45.32.12.2 ftp ftp
This operation will overwrite the current setting and could possibly reboot the system!
Do you want to continue? (y/n)y

Please wait...

Connect to ftp server 45.32.12.2 ...
Get config file from ftp server OK.
File check OK.

Una vez que restauro el respaldo lo tenemos que poner en modo estatico y asignarle una IP

HA-1 # conf sys in

HA-1 (interface) # edit port1

HA-1 (port1) # set mode static

HA-1 (port1) # set ip 192.168.1.33/24

HA-1 (port1) # append allowaccess http

HA-1 (port1) # end


```
Debemos de configurar el otro FG

```
HA-2 # conf sys int

HA-2 (interface) # edit port1

HA-2 (port1) # set mode static

HA-2 (port1) # set ip 192.168.1.32/24

HA-2 (port1) # append allowaccess http

HA-2 (port1) # end

HA-2 # conf sys global

HA-2 (global) # set hostname HA-2

HA-2 (global) # end
```
Ahora ingresamos al FG HA-1 y nos vamos a System->HA y configuramos:
```
Mode:Active-Pasive
Device Priority:130
Group name:FGT (todo el cluster debe tener el mismo nombre)
Pass:123456 (todo el cluster debe tener el mismo pass)

Session pickup:Disable(Sirve el FG primario va a sync las sesiones de los usuairos en los FG del cluster y en caso de que falle el FG primario inicia el secundario y todas las sesiones estarán disponibles)

Monitor Interface:LAN(Port4) (va a revisar que la conexión este disponible, en caso de que el cable se rompa o se apaqgue el equipo lo que va hacer es decirle al FG secundario que ahora el tiene que ser el primario)es important esolo seleccionar las interfaces importantes no todas las LANS, además se puede agregar las VLANs 

Hearbeat Interfaces:HA(Port10) son las interfacees los por el cual los equipos FG se comunican entre sí

Management Interface REservation: 
Interface: MGMT(Port1)
Destination subnet:192.168.0.0/24

```
![118](119.png)

La misma configuración se aplica en el FG 2
![119](120.png)

Nos tenemos que esperar algunos minutos antes de que se hayan sincronizado, al finalizar estaran en verde ambos, en HA1 y HA2.

![120](121.png)

Ahora si abrimos Network-> INterface
veremos que es una copia HA2 de HA1, al igual que otras configuraciones.

![121](122.png)

![122](123.png)

## Configuración Activo-Activo

Modo de balanceo
![123](124.png)

En el laboratorio solo vamos a cambiar en tipo en modo Activo-Activo, en un solo FG y veremos que nos va desconectar momentaneamente y después se sync y si nos vamos al HA-2 veremos que también se configuro como Activo-Activo de forma automaitca en este caso porque procede de una configuración anterior (ACtivo-Pasivo).

![124](125.png)

![125](126.png)

![126](127.png)

Nota: ¿cómo se actualizan los equipos del cluster? Siempre será desde el FG primario.

## Comandos utiles en la CLI en HA
```
HA-1 # diagnose sys ha status
HA information
Statistics
        traffic.local = s:0 p:73974 b:29268286
        traffic.total = s:0 p:79001 b:30076260
        activity.ha_id_changes = 7
        activity.fdb  = c:0 q:0

Model=80001, Mode=1 Group=0 Debug=0
nvcluster=1, ses_pickup=0, delay=0, load_balance=0, schedule=3, ldb_udp=0, upgrade_mode=0.

[Debug_Zone HA information]
HA group member information: is_manage_primary=0.
FGVMEV84EONV3LE8:    Secondary, serialno_prio=1, usr_priority=128, hostname=HA-1
FGVMEVW3TZQZLPA3:      Primary, serialno_prio=0, usr_priority=130, hostname=HA-2

[Kernel HA information]
vcluster 1, state=work, primary_ip=169.254.0.1, primary_id=0:
FGVMEV84EONV3LE8:    Secondary, ha_prio/o_ha_prio=1/1
FGVMEVW3TZQZLPA3:      Primary, ha_prio/o_ha_prio=0/0


HA-1 # HA-1 # diagnose sys ha checksum cluster

================== FGVMEV84EONV3LE8 ==================

is_manage_primary()=0, is_root_primary()=0
debugzone
global: e5 a0 e9 26 88 8c 90 3d 80 b7 9f 3e 67 68 d3 f9 
root: c2 64 6b f0 96 bf c4 4e be 9b bb e6 be 49 25 c2 
all: 9a 64 9b f8 34 33 71 c1 1a 50 f2 ed 11 18 8d 12 

checksum
global: e5 a0 e9 26 88 8c 90 3d 80 b7 9f 3e 67 68 d3 f9 
root: c2 64 6b f0 96 bf c4 4e be 9b bb e6 be 49 25 c2 
all: 9a 64 9b f8 34 33 71 c1 1a 50 f2 ed 11 18 8d 12 

================== FGVMEVW3TZQZLPA3 ==================

is_manage_primary()=1, is_root_primary()=1
debugzone
global: e5 a0 e9 26 88 8c 90 3d 80 b7 9f 3e 67 68 d3 f9 
root: c2 64 6b f0 96 bf c4 4e be 9b bb e6 be 49 25 c2 
all: 9a 64 9b f8 34 33 71 c1 1a 50 f2 ed 11 18 8d 12 

checksum
global: e5 a0 e9 26 88 8c 90 3d 80 b7 9f 3e 67 68 d3 f9 
root: c2 64 6b f0 96 bf c4 4e be 9b bb e6 be 49 25 c2 
all: 9a 64 9b f8 34 33 71 c1 1a 50 f2 ed 11 18 8d 12 

Pasar de HA primario a secundario o viceversa
HA-2 # execute ha manage 0 admin
Could not manage member 0, num=2.

HA-2 # execute ha manage 1 admin
Warning: Permanently added '169.254.0.2' (ED25519) to the list of known hosts.
admin@169.254.0.2's password: 
HA-1 # execute ha manage 0 admin
admin@169.254.0.1's password: 
HA-2 # exit
Connection to 169.254.0.1 closed.

HA-1 # 

Este comando es para volver primario a uno o a otro FG del cluster

HA-2 # execute ha failover set 1
Caution: This command will trigger an HA failover. 
It is intended for testing purposes.
Do you want to continue? (y/n)y


HA-2 # 
Connection lost. Press Enter to start a new session.

Ahora si hacemos un status veremos que equipo esta como primario

HA-2 # get system  ha status
HA Health Status: OK
Model: FortiGate-VM64-KVM
Mode: HA A-A
Group: 0
Debug: 0
Cluster Uptime: 0 days 2:6:2
Cluster state change time: 2023-01-17 14:31:14
Primary selected using:
    <2023/01/17 14:31:14> FGVMEV84EONV3LE8 is selected as the primary because EXE_FAIL_OVER flag is set on peer member FGVMEVW3TZQZLPA3.
    <2023/01/17 12:52:58> FGVMEVW3TZQZLPA3 is selected as the primary because its override priority is larger than peer member FGVMEV84EONV3LE8.
    <2023/01/17 12:52:58> FGVMEVW3TZQZLPA3 is selected as the primary because it's the only member in the cluster.
    <2023/01/17 12:29:05> FGVMEVW3TZQZLPA3 is selected as the primary because its override priority is larger than peer member FGVMEV84EONV3LE8.
ses_pickup: disable
load_balance: disable
load_balance_udp: disable
schedule: Round robin.
upgrade_mode: unset
override: disable
Configuration Status:
    FGVMEVW3TZQZLPA3(updated 3 seconds ago): in-sync
    FGVMEV84EONV3LE8(updated 4 seconds ago): in-sync
System Usage stats:
    FGVMEVW3TZQZLPA3(updated 3 seconds ago):
        sessions=2, average-cpu-user/nice/system/idle=0%/0%/0%/98%, memory=70%
    FGVMEV84EONV3LE8(updated 4 seconds ago):
        sessions=14, average-cpu-user/nice/system/idle=0%/0%/0%/97%, memory=75%
HBDEV stats:
    FGVMEVW3TZQZLPA3(updated 3 seconds ago):
        port10: physical/1000auto, up, rx-bytes/packets/dropped/errors=19221786/55342/0/0, tx=26332232/53748/0/0
    FGVMEV84EONV3LE8(updated 4 seconds ago):
        port10: physical/1000auto, up, rx-bytes/packets/dropped/errors=26331019/53737/0/0, tx=19218309/55350/0/0
MONDEV stats:
    FGVMEVW3TZQZLPA3(updated 3 seconds ago):
        port4: physical/1000auto, up, rx-bytes/packets/dropped/errors=71101/415/0/0, tx=90/1/0/0
    FGVMEV84EONV3LE8(updated 4 seconds ago):
        port4: physical/1000auto, up, rx-bytes/packets/dropped/errors=8724/47/0/0, tx=4018/37/0/0
Secondary   : HA-2            , FGVMEVW3TZQZLPA3, HA cluster index = 0
Primary     : HA-1            , FGVMEV84EONV3LE8, HA cluster index = 1
number of vcluster: 1
vcluster 1: work 169.254.0.2
Secondary: FGVMEVW3TZQZLPA3, HA operating index = 1
Primary: FGVMEV84EONV3LE8, HA operating index = 0

HA-2 # 

Y para revertir ahora es 

HA-2 # execute ha failover unset 1
Caution: This command may trigger an HA failover. 
It is intended for testing purposes.
Do you want to continue? (y/n)y


HA-2 # get system  ha status
HA Health Status: OK
Model: FortiGate-VM64-KVM
Mode: HA A-A
Group: 0
Debug: 0
Cluster Uptime: 0 days 2:9:29
Cluster state change time: 2023-01-17 14:37:31
Primary selected using:
    <2023/01/17 14:37:31> FGVMEVW3TZQZLPA3 is selected as the primary because its override priority is larger than peer member FGVMEV84EONV3LE8.
    <2023/01/17 14:31:14> FGVMEV84EONV3LE8 is selected as the primary because EXE_FAIL_OVER flag is set on peer member FGVMEVW3TZQZLPA3.
    <2023/01/17 12:52:58> FGVMEVW3TZQZLPA3 is selected as the primary because its override priority is larger than peer member FGVMEV84EONV3LE8.
    <2023/01/17 12:52:58> FGVMEVW3TZQZLPA3 is selected as the primary because it's the only member in the cluster.
ses_pickup: disable
load_balance: disable
load_balance_udp: disable
schedule: Round robin.
upgrade_mode: unset
override: disable
Configuration Status:
    FGVMEVW3TZQZLPA3(updated 0 seconds ago): in-sync
    FGVMEV84EONV3LE8(updated 1 seconds ago): in-sync
System Usage stats:
    FGVMEVW3TZQZLPA3(updated 0 seconds ago):
        sessions=2, average-cpu-user/nice/system/idle=1%/0%/3%/88%, memory=70%
    FGVMEV84EONV3LE8(updated 1 seconds ago):
        sessions=0, average-cpu-user/nice/system/idle=0%/0%/1%/96%, memory=74%
HBDEV stats:
    FGVMEVW3TZQZLPA3(updated 0 seconds ago):
        port10: physical/1000auto, up, rx-bytes/packets/dropped/errors=19881964/57013/0/0, tx=27065588/55458/0/0
    FGVMEV84EONV3LE8(updated 1 seconds ago):
        port10: physical/1000auto, up, rx-bytes/packets/dropped/errors=27064294/55444/0/0, tx=19878151/57018/0/0
MONDEV stats:
    FGVMEVW3TZQZLPA3(updated 0 seconds ago):
        port4: physical/1000auto, up, rx-bytes/packets/dropped/errors=71344/416/0/0, tx=90/1/0/0
    FGVMEV84EONV3LE8(updated 1 seconds ago):
        port4: physical/1000auto, up, rx-bytes/packets/dropped/errors=8967/48/0/0, tx=4018/37/0/0
Primary     : HA-2            , FGVMEVW3TZQZLPA3, HA cluster index = 0
Secondary   : HA-1            , FGVMEV84EONV3LE8, HA cluster index = 1
number of vcluster: 1
vcluster 1: work 169.254.0.1
Primary: FGVMEVW3TZQZLPA3, HA operating index = 0
Secondary: FGVMEV84EONV3LE8, HA operating index = 1
```

## IPSec (vpn)

![127](128.png)

![128](129.png)

![129](130.png)

![130](131.png)

![131](132.png)

```
VPN -> Ipsec Wizard->Custom

Name:vSITEb 

Romete Gateway:Static Ip
IP Address:60.89.123.1
Interface: ISP1
NAT:Disable(en este ejemplo no se útiliza)
Dead Peer Detection: on Demand
Forward error conection: no check

Authentication:
Method:Pre-shared  key: password
version:1
mode:Main(ID protection)

Phase 1
Encryption:DES Authentication:check deffe-heltman group(2)
Lifetime:86400

Phase 2 selectors:
name:v2SITEb
Local Address:10.0.1.0/24
Remote Addres:10.0.2.0/24
Phase Proposal:
Encryption:DES Authentication:MD5
Enable Replay Detection: check

Enable Perfect Foeward Secrecey (PFS):
Deffe-Heltman Group: (2)
Local port: all
Remote Port: all
Protocol:all
Auto-negotiation: check
Lifetime:43200

Guardar

También configuramos el FGB
Name:vSITEa 

Romete Gateway:Static Ip
IP Address:200.2112.31.1

Phase 2 selectors:
name:v2SITEb
Local Address:10.0.2.0/24
Remote Addres:10.0.1.0/24
```

![132](133.png)

Después de eso se tiene que crear una ruta estatica
```
Network->Statics Routes

```

![133](134.png)

```
TIP:No es obligatorio pero Es muy recomendable usar, Static Routes:

Network->Static Routes 
Subjet
IP:10.0.2.0/24
Interface:Blackhole
Administrative distance:254 
Click en ok
```

![134](135.png)


Necesitamos agregar una policy con reversa en ambos FG

![135](136.png)

![136](137.png)

##Failover entre 2 tunels

Se hace lo mismo pero ahora con el otro puerto de enlace.

## Autenticación Activa
Para crear el primer ejemplo nos vamos a:
```
User&Autentication->User Definition->Local User->Ponemos un usuario y contraseña->User Account Status

Luego establecemos una politica
Firewall policy-> Aquí ya existe una politica llamada Internet-SDWAN(ya se había establecido con anterioridad) en la parte de source tenemos Active directory pues aquí vamos a agregar a nuestro usarios creado en este caso llamado Sbrv.

```
Como vemos nos pide usario y contraseña para poder navegar
![138](139.png)

## Active directory

En el siguiente ejemplo se realizará a través de un controlador de dominio.

En nuestro laborario, ya existen en el servidor en el grupo user dos usuarios llamado user1,user2 y user3 esto lo vemos desde el active directory user and computers, y dentro de este directorio hay un grupo llamado VIP en el cual están los usuarios 1 y 2, pero no al 3.

![139](140.png)

```
Ahora nos vamos al FG User&Authentication->LDAP Servers->Create New

Name:LDAP
SERVER IP/NAME:10.0.1.10
Server port:389
Common Name Identifier:SAMAccountName
Bind Tyoe: Regular
username:ADATUM\Administrator (dato del servidor)
Pass:Pa$$w0rd (dato del servidor)

Click en Testconnectivity, revisamos que diga Successful

Luego regresamos a en donde dice:Disinguished Name: click en browse y seleccionamos la raiz del dominio o el primer dato mostrado.

click en ok
```
![140](141.png)

```
Ahora en User defition -> Remote LDAP user
Next 
Seleccionamos el servidor LDAP que acabamos de agregar

Y ahora buscamos los usuarios y agregamos los usaurios y click en submit
```
![141](142.png)


```
Después vamos a Firewall policy y source click en editar,user y seleccionamos de la lista LDAP el usario agregado(s)
```
```
Después vamos al servidor y al tratar de navegar nos pide el usuario y contraseña, lo ponemos y nos regresamos a nuestro FG y nos vamos a user&devices y podremos ver el usuario.
```
A veces no redirecciona correctamente a la pagina web así que tenemos que hacer refresh o buscar una pagina y ya no nos pedira el password
![142](143.png)

##  Fortinet Single Sign-On (FSSO)-Usando DC Agent y Collector Agent

### Dc Agent Mode Process(el mas útilzado)
![143](144.png)

Los 2 elementos (1 DC Agent y 2 Collector Agent)se instalan en el controlador de domino

Para instala el sofware en el escritorio del servidior hay un archivo llamado FFSO_Setup ejecutamos nos pedira el usuario del administrador del dominio, corremos con los archivos seleccionador por default siguiente

![146](147.png)

Finish(launch habilitado), siguiente(datos por default si es el mismo equipo que estamos ejecutando o sino la ip correspondiente), en este labortatorio es next en caso real depende que dominios y usuarios deseamos monitorear
![147](148.png)

En esta parte nos cuestiona en que modo deseamos trabajar en este caso DC Agent
![148](149.png)

Nota: Tomar en cuenta que en modo productivo nos solicita reiniciar el equipo.
![149](150.png)

Una vez que sereiniciao buscamos el aplicactivo que se llama configure fortinet sign on

![150](151.png)

Check en Authentication y la password:1234567,Dead entry timeout interval:60
```
Ahora ingresamos al FG-A y nos vamos a Security Fabric->External connectors->Create New->Selec FSSO de FG
```
![151](152.png)
```
Name:AD
Primary fSSO agent:10.0.1.10
Pass:1234567 (debe de ser el passw del programa de FG no el password de administrador del equipo)
user groupsource:Collector Agent
Click en apply &Refresh y nos mostrará la cantidad de usuario o grupos que encontró
```
![152](153.png)
```
Una vez que ya se puso en verde el icono del FFSO nos vamos ahora a
Firewall Policy->vamos a editar el source y seleccionamos la opción User->seleccionamos ADATUM/VIP y click en ok

Ahora cualquier usuario que pertenezca al grupo VIP podra navegar (usuaio 1,2)

ahora vamos al servidor e iniciamos con otro usuario y probamos

Regresamos al forti
Log&Report->eventos->Userevents podremos ver que el usuario 1 ya ingreso

También lo podemos ver desde dashboard->User&Devices->Firewallusers->click en show all FFSO logons

Si hacemos prueba con el usuario 3, no nos dejará navegar.
```
![153](154.png)

![154](155.png)


### Collector Agent-Based Polling Mode Process

![144](145.png)

En esta prueba usamos el mismo external connectors de la anterior prueba.

Para esta practica desde el usaurio administrator en el servidor vamos a desintalar el DC Agent (unistallar DC Agent) pero el collecto lo vamos a dejar, también requiere reinicio del controlador de dominio

Después del reinicio abrimos nuestro Agent configuration y vamos a dar click en Show Monitored DCs, click en Select DC

Ahí seleccionamos Pollig mode y check Windows (WMI) ya que es la más actual y hace un check cada 3 min.

Click en ok y después en Refresh


![155](156.png)
```
Ahora click en set group filter->Add->check Default filter->click en Advance->desplegamos la lista de ADATUM y check en VIP->click en Add selected user groups
```

![156](157.png)


También podemos configurar la opcion ignore user list
click en Add User->user2, aquí no funciona este filtro debido a que el filtro da prioridad a la ip y no al nombre de usuario.

### Agentless Polling Mode Process

![145](146.png)
![157](158.png)
Aquí no hay software 

Para esta pruba vamos a desintalar Fortinet SSOy en la parte de external conectors no necesitamos la anterior ya que vamos a crear uno.

```
External Connector->New->Poll ACtive Directory Server
IP:10.0.1.10
user:adatum\administrator
Password:Pa$$w0rd
LDAP SERVER:lADP (el mismo de clases anteriores.)
Enable poling:enable
user/groups-> click en edit

Aquí podemos escoger un grupo o un usuario.

click en ok, de nuevo se pondrá como activo
```

```
Firewall policy->source->user->Local FSSO Agent-> seleccionamos los dos
```

## Nueva funcionlidad- Configuración save mode

Esto sirve para confirmar los cambios, por ejemplo si cambio una ip y no tiene el resultado deseado, me quedo fuera del equipo y en caso de que este en otro sitio y no dónde se encutra nuestro FG tendría que transladarme hasta ahí y realizar las modificaciones pertinentes pero con esta función de acuerdo al tiempo que le demos para confirmar por ejmplo 1 minuto y no confirmamos restaurará la ip original y volveremos acceder.

```
System-> settings y ahí buscamos configuration save y mode por defecto viene como automatico debemos de cambiarlo a workpspace.

en Timeout: 60(son los segudos que esperará para revertir los cambios) 
```
## Comandos+para+diagnostico


![169](170.png)

```
Como vemos en este ejemplo el mode de conservación de memoria está desactivado

SITE-A # diagnose hardware sysinfo conserve 
memory conserve mode:                        off
total RAM:                                          997 MB
memory used:                                        749 MB   75% of total RAM
memory freeable:                                    141 MB   14% of total RAM
memory used + freeable threshold extreme:           947 MB   95% of total RAM
memory used threshold red:                          877 MB   88% of total RAM
memory used threshold green: 
```
```
si la interface de red tuviera un error lo vemos en
RX erro, tx error, collision 

SITE-A # diagnose hardware deviceinfo nic port1
Name:            port1
Driver:          e1000
Version:         7.3.21-k8-NAPI
Bus:             0000:00:03.0
Hwaddr:          0c:f1:5f:60:00:00
Permanent Hwaddr:0c:f1:5f:60:00:00
State:           up
Link:            up
Mtu:             1500
Supported:       auto 10half 10full 100half 100full 1000full
Advertised:      auto 10half 10full 100half 100full 1000full
Speed:           1000full
Auto:            enabled
RX Ring:                 256
TX Ring:                 256
Rx packets:              110049
Rx bytes:                17643101
Rx compressed:           0
Rx dropped:              0
Rx errors:               102307
  Rx Length err:         102307
  Rx Buf overflow:       0
  Rx Crc err:            0
  Rx Frame err:          0
  Rx Fifo overrun:       0
  Rx Missed packets:     0
Tx packets:              15008
Tx bytes:                5428703
Tx compressed:           0
Tx dropped:              0
Tx errors:               0
  Tx Aborted err:        0
  Tx Carrier err:        0
  Tx Fifo overrun:       0
  Tx Heartbeat err:      0
  Tx Window err:         0
Multicasts:              0
Collisions: 
```
```
SITE-A #diagnose sys top
Run Time:  1 days, 0 hours and 35 minutes
0U, 0N, 1S, 93I, 1WA, 4HI, 0SI, 1ST; 997T, 105F
       ipshelper     7898      S <     0.7     3.6    0
          lnkmtd     7923      S       0.5     1.2    0
            node     7887      S       0.3     6.2    0
       forticron     7895      S       0.3     3.7    0
          httpsd    11447      S       0.1     1.7    0
          fcnacd     7903      S       0.1     1.1    0
  merged_daemons     7891      R       0.1     0.8    0
         cmdbsvr      117      S       0.0     3.7    0
         src-vis     7917      S       0.0     2.8    0
          cw_acd     7947      S       0.0     2.4    0
         reportd     7906      S       0.0     2.3    0
         miglogd     7905      S       0.0     2.1    0
          httpsd     7886      S       0.0     2.1    0
          newcli    11419      S       0.0     2.0    0
           fgfmd     7946      S       0.0     1.9    0
       forticldd     7896      S       0.0     1.8    0
        dnsproxy     7938      S       0.0     1.7    0
         miglogd     7964      S       0.0     1.6    0
 initXXXXXXXXXXX        1      S       0.0     1.6    0
           authd     7897      S       0.0     1.4    0

Para matar un proceso que nos este causando error hacemos
SITE-A #diagnose kill 11 + PID (7897)

```
```
#############################
          IPSEC
#############################

diagnose debug disable
diagnose vpn ike log filter clear
diagnose vpn ike log filter name <phase1-name>
diagnose debug application ike -1
diagnose debug enable

diagnose debug reset
diagnose debug disable

#############################
      Traffic Flow
#############################

diagnose debug disable 
diagnose debug flow trace stop 
diagnose debug flow filter clear 
diagnose debug reset 

diagnose debug flow filter saddr 10.0.1.10
diagnose debug flow filter daddr 8.8.8.8
diagnose debug flow show function-name enable
diagnose debug console timestamp enable
diagnose debug flow trace start 999
diagnose debug enable
```

Nueva funcionalidad CLI
```
Auditar todas las acciones que se ejecutan en la CLI
config sys global
get | group cli
set cli-audit-log enable
end
```


## Port forwarding mediante objetos VIPs
Ejemplo de como trabaja el forwarding de entrada y salida

![158](159.png)

```
Para hacer el siguiente laboratorio tenemos que itnod s 
Policy&Objets->Virtual IPs->Create New->Virtual IP
Name:ISS
Interface ISP1 (Pdemos seleccionar todos any pero aquí solo el ISP1)
Type: Static NAT->ISP1(no permite SDWAN)
External addres/Range:200.212.31.1
MAP To:10.0.1.10 (Es esta IP porqué es el servidor puede ser un servidor de telefonico etc)
Port Forwardo -> Enable->External Service Port 85 MAP to IPv4 port:80 (tiene que ser el puerto real)

```
![150](160.png)
```
Vamos Policy, Creamos una nueva policy o botón derecho sobre el anterio VIPs crado click sobre crear firewall policy using this object
Name:ISS
Incoming Interface:SDWAN-LAB (lo pone por default)
Outgoing:LAN (Poart4)
Source:all
Destination:ISS (lo pone por default)
Service:HTTP
NAT:Disable
```
Si vamos al windows 7 y nos tratamos de conectar al servidor podremos acceder sin problemas

![160](161.png)

En el siguiente laboratorio tenemos que borrar el IPs del proyecto anterior así como las policy.

## Configurar NAT mediante IP Pools

```
Vamos a Policy&Objects->IP POOLS

Name:Pool
Type: existen 4 tipos 
- Overload
-One to One
-Fixed port range
-Port block allocation

*Overload:200.212.31.20-200.212.31.30
Siempre vamos a tener más redes privadas en uso que las públicas.
Muchas direcciones privadas -> pocas redes públicas

aceptamos el objeto y en la policy hacemos lo siguiente:
en donde dice Ip pool configuation->Use Dynamic IP Pool y seleccionamos la pool que nos muestra

ARP:ENABLE(por default)
```
![161](162.png)

Ahora vamos a equipo de internet.
```
Que es un mikrotik
User:admin
Pass:Fortinet1!

y módificamos lo siguiente la IP del indicador 0

[admin@INTERNET] > /ip address
[admin@INTERNET] /ip address> print
Flags: X - disabled, I - invalid, D - dynamic
 #   ADDRESS            NETWORK         INTERFACE
 0   200.212.31.2/30    200.212.31.0    ether2
 1   180.45.22.2/30     180.45.22.0     ether3
 2   60.89.123.2/30     60.89.123.0     ether4
 3   45.32.12.2/30      45.32.12.0      ether5
 4 D 192.168.1.167/24   192.168.1.0     ether1
[admin@INTERNET] /ip address> set address=200.212.31.254/24 numbers=0
[admin@INTERNET] /ip address> print
Flags: X - disabled, I - invalid, D - dynamic
 #   ADDRESS            NETWORK         INTERFACE
 0   200.212.31.254/24  200.212.31.0    ether2
 1   180.45.22.2/30     180.45.22.0     ether3
 2   60.89.123.2/30     60.89.123.0     ether4
 3   45.32.12.2/30      45.32.12.0      ether5
 4 D 192.168.1.167/24   192.168.1.0     ether1
[admin@INTERNET] /ip address>
```
```
Ahora ingresamos a nuestro FG-A ->SD-WAN ->modificamos el gateway del ISP1(port2) y deshabilitamos el ISP2

A continuación nos vamos a nuestro controlador de dominio (server) y probamos la salida a internet y debemos tener salida.

Y podemos rectificarlo en nuestor FG-A con el siguiente comando:
SITE-A # get syste session list
PROTO   EXPIRE SOURCE           SOURCE-NAT       DESTINATION      DESTINATION-NAT 
tcp     3570   10.0.1.10:49378  200.212.31.1:49378 172.217.3.131:443 -               
tcp     3570   10.0.1.10:49395  200.212.31.1:49395 172.217.3.142:443 -               


sin nos fijamos está utilzando en la source nat una de las ip que pusimos en el pool.
```
```
*One-to-One:200.212.31.20-200.212.31.20 (en este modo se utiliza una sola ip, aunque usemos una sola ip debemos expresarlo en modo de rango)

Para esta practica vamos a matar todas las sesiones y para eso nos vamos Dashboard->Fortview sessions->botón derecho->end all sessions

y para matar las sesiones a través de la CLI (FG-A)SITE-A # diagnose sys session clear
```
Ahora vamos agregar un sw y una pc.

![162](163.png)

Y como vemos solo puede navegar el controlador de dominio y no la pc agregadam,ademas en la policy nos indicará que todas las IP han sido utilizadas y no tenemos ni una ip disponible en el pool, ahora si deamos aumentar la ip disponibles:
200.212.31.20-200.212.31.21, podremos salir a internet en ambos equipos
```
*Fixed pport Range:200.212.31.20-200.212.31.21
Internet Ip Range:10.0.1.15-10.0.1.16
es similar a One-to-one la diferencia rádica en que podemos restringir las ip privadas que tendrán salida a internet. 
```

```
*Port Block Allocation:200.212.31.20-200.212.31.21 (ip publicas)
Block Size:128 (cuentos puertos puede utilizar el pool) por defecto son 128
Block Per User:8 (Por cada dirección privda de nuestra red cuantos puertos de esos 128 puede utilizar esa ip, o cuantas sesiones puede crear cada computadora de nuestra lan)
```

## Caso Práctico:IPSEC entre empresa con la misma subet

qué pasa cuando queremos establecer un tunel IPSEC entre dos redes cuya red privada tiene el mismo segmento de red.
ejemplo: 192.168.0.0/24

Lo recomendable es segmetar las redes privadas de cada sitio por ejemplo podemos usar otro:192.168.89.0/24

Entonces para este ejemplo Los FORTIES FG-A y FG-B tendran la mismo gateway 10.0.1.254, así mismo como el controlador de domino (windows server) y la pc (windows 7) 10.0.1.10

```
FG-B
Ahora nos vamos a IPsec Wizard->Custom
Name:Site-A
Remote Gateway:Static IP Address
IP Address:200.212.31.1
Interface:ISP-1(Port2)
NAT Transversal:Disable
Dead Peer Detection:On Demand
DPD retry count:3
DPD retry:20
Pre-sharedkey:1234567
Version:1
Mode: Main(ID Protection)
DES/MD5:2
Ojo, Aquí no funcionaría debido a que son iguales 
Local Address: subnet: 10.0.1.10/24
Remote Address:subnet 10.0.1.10/24

Entonces para solucionar el problema tenemos que asignar IP ficticias y apuntado hacía toda la red local contra toda la red remota

Local Address: subnet: 172.22.0.0/24 (SITE B)
Remote Address:subnet 172.21.0.0/24 (SITE A)

Advance:
DES/MD5:2
Enable Replay Detection:check
Enable perfect forward secrecy (PFS):check
Local Port:all
Remote port :all
Protocol:all
autonegotiate:check
```
```
Ahora configuramos la static route
FG-B
subnet:172.21.0.0/24
Interface:Site-A (el que se creo anteriormente)
```
### Configuración de la policy en el FG-B

```
FG-B IDA
Name:To Site-A
Incoming:LAN(Port4)
Outcoming:Site-A
all
all
all

NAT:Enable
IP Pool Configuration: IPSEC-SITE-B
el secreto está aquí, en vez de usar use outgoing interface addres, debemos seleccionar use Dynamic IP POOL-> Create New IP Pool->Fixed port Range
Name: IPSEC-SITE-B
Type:Fixed Port Range:172.22.0.1-172.22.0.254
Internal IP Range:10.0.1.1-10.0.1.254
ARP:enable
```
```
FG-B Vuelta
Name:From Site-A
Incoming:LAN(Port4)
Outcoming:Site-A (debeía ya estar en verde o levatado el servicio)
Source:all
Destination:create->Virtual IP Server
->Name:IPSEC VIP
Interface:Site-A
Type:Static NAt
External IP address/range:172.22.0.1-172.22.0.254
Map to:10.0.1.1(solo hay que poner la primera IP, el sistema lo llena por dafault hasta el 254)
optional Filter:disable
Port forwarding:disable
botón ok
Service:all

NAT:disable
```


### Ahora lo mismo se hace en el FG-A
```
FG-A
Ahora nos vamos a IPsec Wizard->custom
Name:Site-B
Remote Gateway:Static IP Address
IP Address:60.89.123.1
Interface:ISP-1(Port2)
NAT Transversal:Disable
Dead Peer Detection:On Demand
DPD retry count:3
DPD retry:20
Pre-sharedkey:1234567
Version:1
Mode: Main(ID Protection)
DES/MD5:2

Local Address: subnet: 172.21.0.0/24 (SITE A)
Remote Address:subnet 172.22.0.0/24 (SITE B)

Advance:
DES/MD5:2
Enable Replay Detection:check
Enable perfect forward secrecy (PFS):check
Local Port:all
Remote port :all
Protocol:all
autonegotiate:check
```
```
### Ahora configuramos la static route
FG-A
subnet:172.22.0.0/24
Interface:Site-B (el que se creo anteriormente)
```
### Ahora nos vamos a las policy del FG-A

```
FG-A IDA
Name:To Site-B
Incoming:LAN(Port4)
Outcoming:Site-B
all
all
all

NAT:Enable
IP Pool Configuration: IPSEC SITE A
el secreto está aquí, en vez de usar use outgoing interface addres, debemos seleccionar use Dynamic IP POOL-> Create New IP Pool
Name: IPSEC SITE-A
Type:Fixed Port Range:172.21.0.1-172.21.0.254
Internal IP Range:10.0.1.1-10.0.1.254
ARP:enable
```
```
FG-A Vuelta
Name:From Site-B 
Incoming:LAN(Port4)
Outcoming:Site-B(ya debería estar en verde o habilitado el servicio)
Source:all
Destination:create->Virtual IP Server
->Name:IPSEC VIP
Interface:Site-B
Type:Static NAT
External IP address/range:172.21.0.1-172.21.0.254
Map to:10.0.1.1(solo hay que poner la primera IP, el sistema lo llena por dafault hasta el 254)
optional Filter:disable
Port forwarding:disable
botón ok

Service:all

NAT:disable

```

