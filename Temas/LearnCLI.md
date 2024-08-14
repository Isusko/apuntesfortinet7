# Aprendiendo a usar CLI
? <- Si apretamos nos mostrará el menú

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