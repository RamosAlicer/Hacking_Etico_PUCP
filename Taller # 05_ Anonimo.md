# Tor-Proxychains
<b>1. Introducción:</b>
<br>
Proxychains es una herramienta que obliga a cualquier conexión TCP realizada por cualquier aplicación a seguir a través de un proxy como TOR o cualquier otro proxy SOCKS4, SOCKS5 o HTTP(S)..
<br>
<BR>
<B>2. Que es SOCKS?</B>
<BR>
SOCKS es un protocolo de Internet que intercambia paquetes de red entre un cliente y un servidor a través de un servidor proxy.

<b>Requirimientos:</b>
<br>
1. Tor

2. Proxychains
<br>

<b>Instalación:</b>
<br>
  
Paso 1: Instale con el siguiente comando:
  ```
    sudo apt install tor
   ``` 
![Tor](https://user-images.githubusercontent.com/120317751/213859515-ef8c9107-b946-45fb-a75d-620ce06c14d1.gif)

Paso 2: instalar Proxychains con el siguiente comando
```
    sudo apt install proxychains   
```

![Proxychains](https://user-images.githubusercontent.com/120317751/213860469-a9916cd1-a6ae-4881-8328-0d8909049726.gif)

Paso 3: Habilite el servicio tor con el siguiente comando
```
    sudo service tor start
 ```   
    
![Screenshot from 2023-01-23 08-23-42](https://user-images.githubusercontent.com/120317751/213958487-3530288c-fff4-45a3-bf31-a9bf81199314.png)

Paso 4 : Verifique el estado del servicio tor con el comando siguiente
```
   sudo service tor status
 ```  

![Screenshot from 2023-01-23 08-26-51](https://user-images.githubusercontent.com/120317751/213958784-84797fd3-3cfd-4e21-94c7-3decff07a3e2.png)

Paso 5: Configurar ProxyChains mediante el archivo Proxychains.conf
```
     sudo nano /etc/proxychains.conf
 ``` 
  
![Tor](https://user-images.githubusercontent.com/120317751/213960159-e0e265a5-670c-41bd-bdb6-750601bd9551.gif)
  
  
Descomentar borrando el # la línea dynamic_chain, de esta forma, la cadena será dinámica.
Comentar con un # la línea strict_chain, de esta forma, la cadena no será estricta.
  
 ``` 
     #Dynamic Chain -----> Dynamic Chain
  
     Strict Chain -------> #Strict Chain
  
     #Random Chain -------> Random Chain 
  
     ##Proxy DNS requests - no leak for DNS data ---> Proxy DNS requests - no leak for DNS data
  ```
Paso 6: Ahora tendremos que realizar una configuración adicional muy importante, y es la de configurar el SOCKS5 que nosotros queremos, para realizar esto, deberemos situar en la parte inferior esta configuración
 ``` 
      socks5  127.0.0.1 9050
  ```
  
  
![Screenshot from 2023-01-23 08-55-48](https://user-images.githubusercontent.com/120317751/213961008-c6814422-bdfa-4662-a0df-9d6fd2bd7fa3.png)


Paso 7: Reiniciar el sistema
```
       reboot
  ``` 
   
Paso 8: Inciar tor y luego proxychains
```
  sudo service tor start
       proxychains firefox whatismyipaddress.com
  ``` 
   
   
![Screencast from 2023-01-23 09-16-01](https://user-images.githubusercontent.com/120317751/213963120-9d7e6406-fcaf-4de7-a30e-6c380f704d4b.gif)
   
   
   
   
   
   

    








