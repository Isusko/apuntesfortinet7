# Bloquear navegación a otros países

 ```
 Para esto tenemos que crear dos objetos

 Policy&Objects->Address->Create New->
 Name:China
 Type:Geografico
 Contry/Región:China(Seleccionamos cualquier país)

 Ahora Creamos un grupo

 Create New Group
 Name:Países Bloqueados
 Type:Group
 Member: China(Creado en paso anterior)

 ```
 Ahora creamos una policy

 ```
Incoming:LAN4
Outgoing:
Source:
Aquí podemos agregar cualquier equipo local o en este ejemplo se bloquean camaras:
Name:h 10.0.1.14
Ip:10.0.1.14/32


Destination:Paises Bloqueados
Service:all
Action: Deny
Log Violation Trafic:Enable

 ```