# Nuestra primer policy vía web
Estos pasos se realizan en ambos dispositivos forti

Policy objets->Firewall Policy
![32](/img/33.png)
Como vemos ha sido creado
![33](/img/34.png)

Ahora si hacemos un ping en la pc1 vemos que ahora si lo hace:
![34](/img/35.png)

# Nuestra primer policy vía CLI
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