# WebProg 2. Házi Feladat – Dokumentáció

**Neptun ID:** PAPZBG  
**Szerver:** h-37-221-211-198 (37.221.211.198)  
**Domain:** PAPZBG.webprog.fun  
**Dátum:** 2026-03-02

---

## 1. A két futtatandó szerver

### server.py (nagy Python szerver)
Az 1. háziból örökölt Python webszerver, amely statikus fájlokat szolgál ki a `/var/www/` mappából.

**Módosítások:**
- `HOST = "127.0.0.1"` – csak localhoston figyel, kívülről közvetlenül nem érhető el (security)
- A `main()` függvényből eltávolítottam a HTTPS szálat – az nginx kezeli a TLS-t

**Systemd service** (`/etc/systemd/system/webprog.service`):
```ini
[Unit]
Description=WebProg Python Server
After=network.target

[Service]
ExecStart=/usr/bin/python3 /root/webprog_py_server/server.py
WorkingDirectory=/root/webprog_py_server
Restart=always
User=root

[Install]
WantedBy=multi-user.target


infoserver.py (kispython szerver)
A 2. gyakorlaton látott kis Python szerver, amely a kliens IP-jét, a szerver hostname-jét, PID-jét és portját adja vissza.

Systemd service (/etc/systemd/system/infoserver.service):

[Unit]
Description=Kispython Info Server
After=network.target

[Service]
ExecStart=/usr/bin/python3 /root/infoserver.py 9090
Restart=always
User=root

[Install]
WantedBy=multi-user.target

Démonok engedélyezése és indítása:

systemctl daemon-reload
systemctl enable --now webprog infoserver
```
2. Nginx konfiguráció
Az nginx a 80-as porton figyel és két útvonal között válogat:

/static/ → átirányítja a kéréseket a nagy Python szerverre (8080-as port), az URL-t /file/-ra képezi le
/app → átirányítja a kéréseket a kispython szerverre (9090-es port)
/etc/nginx/sites-enabled/default:
```
server {
    listen 80;
    server_name 37.221.211.198 PAPZBG.webprog.fun;

    location /static/ {
        proxy_pass http://127.0.0.1:8080/file/;
    }

    location /app {
        proxy_pass http://127.0.0.1:9090/;
    }
}
```
Megjegyzés: a proxy_pass-ban a /file/ végén lévő slash miatt az nginx levágja a /static/ prefixet és /file/-ra cseréli, így pl. a /static/kep.png kérés a Python szerverhez /file/kep.png-ként érkezik meg.

3. Domain – DNS rekordok
A webprog.fun zónát a Cloudflare névszerverei szolgálják ki.

SOA rekord:

webprog.fun.  1800  IN  SOA  kristin.ns.cloudflare.com. dns.cloudflare.com. 2397502814 10000 2400 604800 1800

NS rekordok:

webprog.fun.  1632  IN  NS  leonidas.ns.cloudflare.com.
webprog.fun.  1632  IN  NS  kristin.ns.cloudflare.com.

Működés tesztelése:

root@h-37-221-211-198:~# curl https://PAPZBG.webprog.fun/app
Client IP: 127.0.0.1
Server hostname: h-37-221-211-198
Process ID: 1087314
Listening port: 9090

## 4. HTTPS – Let's Encrypt

A tanúsítványt a **Let's Encrypt** szolgáltatás biztosítja, a **Certbot** nginx pluginján keresztül telepítve.

```bash
apt install certbot python3-certbot-nginx -y
certbot --nginx -d PAPZBG.webprog.fun
```
A certbot automatikusan módosította az nginx konfigurációt:
-  Tanúsítvány telepítve
-  HTTP → HTTPS redirect beállítva

---

## Megjegyzés

A feladat megoldásához **Claude AI** ([claude.ai](https://claude.ai)) segítségét vettem igénybe a konfigurációs lépések során.
