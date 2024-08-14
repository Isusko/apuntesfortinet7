## Configuración de interfaces

Primero vamos a trabajar con el Site A.
Puerto 2 sera nuestro ISP1 (La IP es la que nos muestra en el laboratorio)

![22](/img/23.png)

Puerto 3 (La IP es la que nos muestra en el laboratorio)

![23](/img/24.png)

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
![24](/img/25.png)

Como podemos ver es agregar el gateway (sin mascara)de los proveedores anteriormente añadidos
![25](/img/26.png)

En esta segunda configuración en la opción de distancia la ponemos más alta, eso se debe a que uno será la red principal(10) y la otra (15) el backup en caso de que se caiga o falle la red entra la otra en función. 
![26](/img/27.png)

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

![27](/img/28.png)

Ahora nos conectamos a la pc y hacemos la pruba de ping al 10.0.1.254

![28](/img/29.png)

![29](/img/30.png)

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