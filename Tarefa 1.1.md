# Tarefa 1.1
---
## 1 Comprobación de que funciona como servidor DNS, comando *dig @localhost www.edu.xunta.es NS*

```
root@darthvader:/# dig @localhost www.edu.xunta.es NS

; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> @localhost www.edu.xunta.es NS
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37926
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 7fb5f1c37d15905f0100000068e618811c07eca6f7e27522 (good)
;; QUESTION SECTION:
;www.edu.xunta.es.		IN	NS

;; ANSWER SECTION:
www.edu.xunta.es.	1016	IN	CNAME	edu.xunta.es.

;; AUTHORITY SECTION:
xunta.es.		1800	IN	SOA	a1-15.akam.net. sistemas.xunta.es. 2018113332 10800 1800 1814400 1800

;; Query time: 61 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Wed Oct 08 07:53:37 UTC 2025
;; MSG SIZE  rcvd: 154
```

---

## 2 Configuración reenviadores

### Documento *named.conf.options*

```
options {
	directory "/var/cache/bind";

	forwarders {
        8.8.8.8;
    };

	dnssec-validation no;

	listen-on-v6 { none; };
}; 
```

### Salida del comando *dig @localhost www.mecd.gob.es NS*

```

root@darthvader:/# dig @localhost www.mecd.gob.es NS

; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> @localhost www.mecd.gob.es NS
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 10637
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 0c6f9796787bfdd40100000068e6184a2dc389689d2e47df (good)
;; QUESTION SECTION:
;www.mecd.gob.es.		IN	NS

;; AUTHORITY SECTION:
mecd.gob.es.		1792	IN	SOA	ns1.age.gob.es. dns.cloudflare.com. 2384331704 10000 2400 604800 1800

;; Query time: 13 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Wed Oct 08 07:52:42 UTC 2025
;; MSG SIZE  rcvd: 140
```

---

## 3 Instalar unha zona primaria de resolución directa, arquivos named.conf.local e do arquivo de zona db.starwars.lan

### named.conf.local
```
zone "starwars.lan" IN {
    type master;
    file "/etc/bind/db.starwars.lan
}
```
### db.starwars.lan
```
;
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	darthsidious.starwars.lan. admin.starwars.lan. (
			      1		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;

@	IN	NS	darthsidious.starwars.lan.

darthvader	IN	A	192.168.20.10
skywalker   IN  A   192.168.20.101
skywalker   IN  A   192.168.20.111
luke    IN  A   192.168.20.22
darthsidious    IN  A   192.168.20.11
yoda    IN  A   192.168.20.24
yoda    IN  A   192.168.20.25
c3p0    IN  A   192.168.20.26

palpatine   IN  CNAME   darthsidious

@   IN  MX 10   c3p0

lenda   IN  TXT "Que a forza te acompanhe"

```
---

## 4 Instalar la resolucion inversa, aruivos named.conf.local e db.20.168.192

### named.conf.local
```
zone "starwars.lan" IN {
    type master;
    file "/etc/bind/db.starwars.lan
}
zone "20.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.20.168.192";
};
```
### db.20.168.192
```
$TTL    604800
@       IN      SOA     darthsidious.starwars.lan. admin.starwars.lan. (
                        1               ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@        IN      NS      darthsidious.starwars.lan.

10      IN      PTR     darthvader.starwars.lan.
101     IN      PTR     skywalker.starwars.lan.
111     IN      PTR     skywalker.starwars.lan.
22      IN      PTR     luke.starwars.lan.
11      IN      PTR     darthsidious.starwars.lan.
24      IN      PTR     yoda.starwars.lan.
25      IN      PTR     yoda.starwars.lan.
26      IN      PTR     c3p0.starwars.lan.
```
---

## 5 Comprobación de funcionamento


### nslookup darthvader.starwars.lan localhost
```
root@dnsserver:/# nslookup darthvader.starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

Name:	darthvader.starwars.lan
Address: 192.168.20.10
```
### nslookup skywalker.starwars.lan localhost
```
root@dnsserver:/# nslookup darthvader.starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

Name:	darthvader.starwars.lan
Address: 192.168.20.10

```
### nslookup starwars.lan localhost
```
root@dnsserver:/# nslookup starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

Name:	starwars.lan
Address: 192.168.20.100

```
### nslookup -q=mx starwars.lan localhost
```
root@dnsserver:/# nslookup -q=mx starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

starwars.lan	mail exchanger = 10 c3p0.starwars.lan.
```
### nslookup -q=ns starwars.lan localhost
```
root@dnsserver:/# nslookup -q=ns starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

starwars.lan	nameserver = darthsidious.starwars.lan.

```
### nslookup -q=soa starwars.lan localhost
```
root@dnsserver:/# nslookup -q=soa starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

starwars.lan
	origin = darthsidious.starwars.lan
	mail addr = admin.starwars.lan
	serial = 1
	refresh = 604800
	retry = 86400
	expire = 2419200
	minimum = 604800

```
### nslookup -q=txt lenda.starwars.lan localhost
```
root@dnsserver:/# nslookup -q=txt lenda.starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

lenda.starwars.lan	text = "Que a forza te acompanhe"

```
### nslookup 192.168.20.11 localhost
```
root@dnsserver:/# nslookup 192.168.20.11 localhost
11.20.168.192.in-addr.arpa	name = darthsidious.starwars.lan.

```
---

