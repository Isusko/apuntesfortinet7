# SD-WAN (Software Define WAN - Acceso a internet definido por software)

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

![80](/img/81.png)

En la opción de Interface members click en plus (+) y en la opción seleccionar entrada click en CREATE  y rellenamos los datos solicitados


```
INTERFACE:ISP1(Port2)
SD-WAN Zone:SDWAN-LAN
Gateway:200.212.31.12

Click en ok
```
![81](/img/82.png)

```
INTERFACE:ISP2(Port3)
SD-WAN Zone:SDWAN-LAN
Gateway:180.45.22.2

Click en ok
```
![82](/img/83.png)

Ahora se tienen que crear una ruta estatica y ponemos los datos solicitados
```
Subnet:0.0.0.0/0.0.0.0
Interface:SD-WAN-LAN 
Estatus:Enabled

Click en ok
```
![83](/img/84.png)

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
![84](/img/85.png)

ahora probamos en el servidor el internet con un ping y con el navegador y verificamos que funciona.

## SD-WAN Performance SLAs
Son testigos para probar nuestos ISP's esten funcionando correctamente y no esten caidos, ya hay algunas opciones como la de amazon.
con