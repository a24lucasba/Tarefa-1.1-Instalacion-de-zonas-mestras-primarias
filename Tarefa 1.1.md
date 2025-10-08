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

## 3 Instalar unha zona primaria de resolución directa