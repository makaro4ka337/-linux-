# -linux sinni home-
Минимальная маска сети 3O 
32 адреса  – 27 маска сети 
16 - 28
8 – 29
4 – 3O
172.16.1.0 – адрес сети
Машина ISP
hostnamectl set-hostname isp.au-team.irpo

iptables –t nat –A POSTROUTING –o ens3 –j MASQUERADE
iptables-save >> /etc/sysconfig/iptables
systemctl enable --now iptables

Машина HQ-RTR
Настройка доменного имени
en
conf t
hostname HQ-RTR
ip domain-name au-team.irpo
end 
write memory

Настройка GRE
en
conf t
interface tunnel.0
ip address 10.10.10.1/30 (смотреть в задании
ip tunnel 172.16.1.2 172.16.2.2 mode gre (смотреть в задании Настройте интерфейс, в сторону HQ-RTR
end 
write memory

Настройка статической маршрутизации 
conf t
ip route 0.0.0.0 0.0.0.0 172.16.1.1 Настройка интерфейс, в сторону HQ-RTR
end 
write memory

Настройка динамической маршрутизации (OSPF)
en
conf t
router ospf 0
network 10.10.100.0/27 area 0 (vlan 1OO
network 10.10.200.0/28 area 0 (vlan 2OO
network 10.10.30.0/29 area 0 (vlan 999
network 10.10.10.0/30 area 0 (gre тунель
passive-interface default
no passive-interface tunnel.0
area 0 authentication
exit
interface tunnel.0
ip ospf authentification message-digest 
ip ospf message-digest-key 1 md5 P@ssw0rd 
end 
write memory


Настройка динамической трансляции адресов (NAT)
en
conf t
interface isp
ip nat outside
exit
interface vl100
ip nat inside
exit
interface vl200
ip nat inside
exit
interface vl999
ip nat inside
exit
ip nat pool HQ 10.10.0.1-10.10.200.254 ( число 1O повторяющиеся, третье число разное, поэтому O и цифра 1 в конце т.к О быть не может. (число 2ОО т.к самое макимальное в 3 числе после точки и 254 самое максимальное и возможных
ip nat source dynamic inside-to-outside pool HQ overload interface isp
end
write memory

Настройка часового пояса
en
conf t
ntp timezone utc+3





Машина BR-RTR
Настройка доменного имени
en
conf t
hostname BR-RTR
ip domain-name au-team.irpo
end 
write memory

Настройка GRE
en
conf t
interface tunnel.0
ip address 10.10.10.2/30 ( в hq на конце было 1, тут 2
ip tunnel 172.16.2.2 172.16.1.2 mode gre (меняю местами айпишник с hq-rtr
end 
write memory

Настройка статической маршрутизации 
conf t
ip route 0.0.0.0 0.0.0.0 172.16.2.1 (Настройте интерфейс, в сторону BR-RTR
end 
write memory
