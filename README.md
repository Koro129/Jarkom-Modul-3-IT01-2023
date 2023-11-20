# Jarkom-Modul-3-IT01-2023

# Topologi

**Aura - (DHCP Relay)**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.64.1.212
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.64.2.212
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.64.3.212
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.64.4.212
	netmask 255.255.255.0
```

**Himmel - (DHCP Server)**
```
auto eth0
iface eth0 inet static
	address 10.64.1.1
	netmask 255.255.255.0
	gateway 10.64.1.212
```

**Heiter - (DNS Server)**
```
auto eth0
iface eth0 inet static
	address 10.64.1.2
	netmask 255.255.255.0
	gateway 10.64.1.212
```

**Denken - (Database Server)**
```
auto eth0
iface eth0 inet static
	address 10.64.2.1
	netmask 255.255.255.0
	gateway 10.64.2.212
```

**Eisen - (Load Balancer)**
```
auto eth0
iface eth0 inet static
	address 10.64.2.2
	netmask 255.255.255.0
	gateway 10.64.2.212
```

**Frieren - (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.4.1
	netmask 255.255.255.0
	gateway 10.64.4.212
```

**Flamme - (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.4.2
	netmask 255.255.255.0
	gateway 10.64.4.212
```

**Fern - (Laravel Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.4.3
	netmask 255.255.255.0
	gateway 10.64.4.212
```

**Lawine - (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.3.3
	netmask 255.255.255.0
	gateway 10.64.3.212
```

**Linie - (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.3.2
	netmask 255.255.255.0
	gateway 10.64.3.212
```

**Lugner - (PHP Worker)**
```
auto eth0
iface eth0 inet static
	address 10.64.3.1
	netmask 255.255.255.0
	gateway 10.64.3.212
```

**Revolte, Richter, Sein, dan Stark - (Client)**
```
auto eth0
iface eth0 inet dhcp
```
