# Habilitar Disclaimer
```
Esto se hace desde la firewall policy que nos da salida a internet y solo lo permite habilitarlo a tráves de la CLI.

SITE-B # config firewall policy 

SITE-B (policy) # show
config firewall policy
    edit 1
        set name "INTERNET-SDWAN"
        set uuid 3e1eb46a-abf3-51ed-1b08-5cf62ee2be4e
        set srcintf "port4"
        set dstintf "SD-WANInternet"
        set action accept
        set srcaddr "all"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"
        set nat enable
    next
end

SITE-B (policy) # edit 1



SITE-B (1) # set disclaimer??
enable     Enable user authentication disclaimer.
disable    Disable user authentication disclaimer.
 
SITE-B (1) # set disclaimer enable 

SITE-B (1) # show
config firewall policy
    edit 1
        set name "INTERNET-SDWAN"
        set uuid 3e1eb46a-abf3-51ed-1b08-5cf62ee2be4e
        set srcintf "port4"
        set dstintf "SD-WANInternet"
        set action accept
        set srcaddr "all"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"
        set nat enable
        set disclaimer enable
    next
end

SITE-B (1) # end

```
Este mensaje se puede modificar y también el logging
system->Replacemente Msjs y hay una lista 
Disc->disclaimer, Loggin Page : es una configuración
HTML que podemos modificar

 ![166](/img/167.png)