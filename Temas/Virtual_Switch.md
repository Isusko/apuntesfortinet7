# Virtual Switch 

![101](/img/102.png)

![102](/img/103.png)

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

![103](/img/104.png)

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