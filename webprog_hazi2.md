# WEB PROG HÁZI 2 - setting up reverse proxy on my server

## 1. feladat - PYTHON SERVER DEAMONként futtatása
 ### A python webszervert systemd service-ként futtattam.
<pre> "bash sudo systemctl status webprog-pyserver " </pre>
 ### majd létrehoztam egy felhasználót:

<pre> "sudo useradd --system --no-create-home --shell /usr/sbin/nologin webprog" </pre>

  ### áthelyeztem a szervert:
<pre> "sudo mkdir -p /opt/webprog_py_server
sudo rsync -a /root/webprog_py_server/ /opt/webprog_py_server/
sudo chown -R webprog:webprog /opt/webprog_py_server "</pre>

### systemd service létrehozása:
Fájl: /etc/systemd/system/webprog-pyserver.service
<pre>
[Unit]
Description=WebProg Python Server (homework)
After=network.target

[Service]
Type=simple
User=webprog
Group=webprog
WorkingDirectory=/opt/webprog_py_server
ExecStart=/usr/bin/python3 /opt/webprog_py_server/server.py
Restart=on-failure
RestartSec=2

[Install]
WantedBy=multi-user.target
</pre>

### sudo apt update
### service inditása és engedélyezése
<pre> sudo systemctl daemon-reload
sudo systemctl start webprog-pyserver
sudo systemctl enable webprog-pyserver </pre>

### ellenőrzések:
*systemctl status*
<pre> root@h-37-221-211-198:~# sudo systemctl status webprog-pyserver --no-pager
● webprog-pyserver.service - WebProg Python Server (homework)
     Loaded: loaded (/etc/systemd/system/webprog-pyserver.service; enabled; preset: enabled)
     Active: active (running) since Tue 2026-02-24 19:24:49 CET; 14s ago
   Main PID: 185732 (python3)
      Tasks: 3 (limit: 2348)
     Memory: 13.9M
        CPU: 127ms
     CGroup: /system.slice/webprog-pyserver.service
             └─185732 /usr/bin/python3 /opt/webprog_py_server/server.py
</pre>

  *curl -i*
  <pre> root@h-37-221-211-198:~# curl -i http://127.0.0.1:8080/
HTTP/1.0 404 Not Found
Server: WebProgPython/1.1 Python/3.11.2
Date: Tue, 24 Feb 2026 18:31:54 GMT
X-Content-Type-Options: nosniff
Cache-Control: no-store
Connection: close
Content-Type: text/plain; charset=utf-8

404 Not Found
root@h-37-221-211-198:~# curl -i http://127.0.0.1:8081/
curl: (7) Failed to connect to 127.0.0.1 port 8081 after 0 ms: Couldn't connect to server
root@h-37-221-211-198:~# curl -i http://127.0.0.1:8443/
curl: (56) Recv failure: Connection reset by peer
root@h-37-221-211-198:~# curl -k -i https://127.0.0.1:8443/
HTTP/1.0 404 Not Found
Server: WebProgPython/1.1 Python/3.11.2
Date: Tue, 24 Feb 2026 18:33:13 GMT
X-Content-Type-Options: nosniff
Cache-Control: no-store
Connection: close
Content-Type: text/plain; charset=utf-8

404 Not Found
root@h-37-221-211-198:~# grep -n "def do_GET" -n /opt/webprog_py_server/server.py
grep -n "case " /opt/webprog_py_server/server.py
grep -n "if self.path" /opt/webprog_py_server/server.py
215:    def do_GET(self) -> None:
root@h-37-221-211-198:~# grep -n "/student" /opt/webprog_py_server/server.py
192:        if path_only == "/student":
root@h-37-221-211-198:~# curl -i http://127.0.0.1:8080/student
curl -i http://127.0.0.1:8080/
curl -i http://127.0.0.1:8080/app/
HTTP/1.0 200 OK
Server: WebProgPython/1.1 Python/3.11.2
Date: Tue, 24 Feb 2026 18:34:34 GMT
X-Content-Type-Options: nosniff
Cache-Control: no-store
Connection: close
X-Display-Name: Duri
X-Neptun-ID: PAPZBG
Content-Type: text/plain; charset=utf-8

OK
HTTP/1.0 404 Not Found
Server: WebProgPython/1.1 Python/3.11.2
Date: Tue, 24 Feb 2026 18:34:34 GMT
X-Content-Type-Options: nosniff
Cache-Control: no-store
Connection: close
Content-Type: text/plain; charset=utf-8

404 Not Found
HTTP/1.0 404 Not Found
Server: WebProgPython/1.1 Python/3.11.2
Date: Tue, 24 Feb 2026 18:34:34 GMT
X-Content-Type-Options: nosniff
Cache-Control: no-store
Connection: close
Content-Type: text/plain; charset=utf-8

404 Not Found
root@h-37-221-211-198:~#

Feb 24 19:24:49 h-37-221-211-198 systemd[1]: Started webprog-pyserver.service - WebProg Python Server (homework). </pre>


A TÖBBIT FOLYTATOM, csak a kód debuggolása felhúzott :D
