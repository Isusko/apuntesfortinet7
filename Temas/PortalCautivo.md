# Portal Cautivo en Fortinet
Esta cofiguración solo funciona para el puerto que nos de salida a internet en este laboratio se hace todos a través del puerto 4.

Primero tenemos que crear un usuairo y/o grupo con autenticación activa aquí no se puede usar la autenticación pasiva.
```
User&Authentication->user defitinition->crear usuario/password
Elias
Pass:1234567

Ahora el grupo
user Groups->Internet->Firewall y agregamos al gruupo al usuairo anterior
```
Ahora si activamos portal cautivo/Captive Portal

```
Interface->Puerto 4-> Activar Captive Portal-> en la autenticación del portal-> seleccionamos local->en user acces->Allow all
user group:Internet  (grupo creado en paso anterior)
Exempt Source:Podemos dar exepcioines localas
Exempot destination/server: Por ejemplo google correo o office, aquí al ingresar a esos servios no nos solicitará contraseña

Click en ok
```