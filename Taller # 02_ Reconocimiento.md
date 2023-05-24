### Introducción

Visitemos el sitio web de MegaCorp One ( https://www.megacorpone.com/ ) para obtener más información sobre nuestro objetivo.e


![1](https://user-images.githubusercontent.com/50930193/116324320-8c398180-a785-11eb-903a-8fa59d328201.jpg)

Una revisión rápida de su sitio web revela que son una empresa de nanotecnología. La página Acerca de en https://www.megacorpone.com/about.html revela direcciones de correo electrónico y cuentas de Twitter de algunos de sus empleados:

![2](https://user-images.githubusercontent.com/50930193/116324339-9491bc80-a785-11eb-969a-98e57d02443b.jpg)


Podríamos usar estas direcciones en una campaña de recopilación de información en las redes sociales. También vale la pena mencionar que el formato de la dirección de correo electrónico de la empresa sigue un patrón de "letra inicial del nombre mas el apellido"s  Sin embargo, la dirección de correo electrónico de su CEO simplemente usa su nombre de pila. Esto indica que los fundadores o los empleados de larga data tienen un formato de correo electrónico diferente al de las contrataciones más nuevas. Esta información podría ser útil en una etapa posterior de la evaluación.

Lo otro es que se tiene acceso a las redes sociales corporativas, que se encuentran en la misma página. No tendría nada de malo, pero es un dato a considerar.

![3](https://user-images.githubusercontent.com/50930193/116324498-e0446600-a785-11eb-8f98-b9d00f653aa3.jpg)


### Extracting subdomains with BASH 

En este ejemplo, queremos encontrar todos los subdominios enumerados en la página web principal de megacorpone.com y encontrar sus direcciones IP correspondientes. Hacer esto manualmente sería frustrante y llevaría mucho tiempo, pero con algunos comandos básicos de Bash, podemos convertirlo en una tarea fácil. Comenzaremos descargando la página de índice con wget:

``` 
wget www.megacorpone.com
```
Descargamos los archivos

```
--2021-05-04 16:06:06--  http://www.megacorpone.com/
Resolving www.megacorpone.com (www.megacorpone.com)... 149.56.244.87
Connecting to www.megacorpone.com (www.megacorpone.com)|149.56.244.87|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 14603 (14K) [text/html]
Saving to: ‘index.html’

index.html                              100%[==============================================================================>]  14.26K  --.-KB/s    in 0.1s    

2021-05-04 16:06:06 (127 KB/s) - ‘index.html’ saved [14603/14603]
```
Podemos listar los archivos

```
ls -l index.html
```
Revisemos el resultado
```
-rw-r--r-- 1 root root 14603 Nov  6  2019 index.html
```

Escaneando manualmente el archivo, vemos muchas líneas que no necesitamos. Comencemos a reducir las líneas que necesitamos y eliminemos las líneas que no necesitamos. Primero, podemos usar grep “href=” para extraer todas las líneas en index.html que contienen enlaces HTML:

```
grep "href=" index.html
```
![4](https://user-images.githubusercontent.com/50930193/117063924-1edca200-aceb-11eb-8290-2998508c99df.jpg)

En el extracto anterior, el texto marcado es un ejemplo de lo que estamos buscando, ya que hace referencia a un subdominio.

Usemos grep para tomar líneas que contengan “.megacorpone”, indicando la existencia de un subdominio, y grep -v para quitar líneas que contengan el aburrido dominio “ www.megacorpone.com” que ya conocemos:

```
grep "href=" index.html | grep "\.megacorpone" | grep -v "www\.megacorpone\.com" | head
```
![5](https://user-images.githubusercontent.com/50930193/117064318-a2968e80-aceb-11eb-93a0-c804bbf5725c.jpg)

Esta salida se parece más a lo que necesitamos. Al reducir nuestros datos de manera lógica y hacer reducciones secuencialmente más pequeñas con cada paso, estamos en medio del ciclo más común en el manejo de datos.

Parece que cada línea contiene un enlace y un subdominio, pero debemos deshacernos del HTML adicional alrededor de nuestros enlaces. Siempre hay múltiples enfoques para cualquier tarea realizada en Bash, pero usaremos uno poco conocido para esto. Usaremos la opción -F de awk para establecer un delimitador de varios caracteres, a diferencia de cut, que es simple y práctico pero solo permite delimitadores de un solo carácter. Estableceremos nuestro limitador en http:// y le diremos a awk que queremos el segundo campo ('{print $2}'), o todo después de ese delimitador:

```
grep "href=" index.html | grep "\.megacorpone" | grep -v "www\.megacorpone\.com" | awk -F "http://" '{print $2}'
```
Obtenemos una mejor lista
```
admin.megacorpone.com/admin/index.html">Cell Regeneration</a></li>
intranet.megacorpone.com/pear/">Immune Systems Supplements</a></li>
mail.megacorpone.com/menu/">Micromachine Cyberisation Repair</a></li>
mail2.megacorpone.com/smtp/relay/">Nanomite Based Weaponry Systems</a></li>
siem.megacorpone.com/home/">Nanoprobe Based Entity Assimilation</a></li>
support.megacorpone.com/ticket/requests/index.html">Nanoprocessors</a></li>
syslog.megacorpone.com/logs/sys/view.php">Perlin VanHook Chemical Dispersal</a></li>
test.megacorpone.com/demo/index.php">What are the ethics behind MegaCorp One?</a></li>
vpn.megacorpone.com/diffie-hellman/">Is MegaCorp One being regulated by any goverment?</a></li>
...
```

El comienzo de cada línea en nuestra salida muestra que estamos en el camino correcto. Ahora, podemos usar cortar o establecer el delimitador en "/" (con -d) e imprimir el primer campo (con -f 1), dejándonos solo con el nombre completo de ubdomain:

```
grep "href=" index.html | grep "\.megacorpone" | grep -v "www\.megacorpone\.com" | awk -F "http://" '{print $2}' | cut -d "/" -f 1
```
Obtenemos una mejor lista
```
admin.megacorpone.com
intranet.megacorpone.com
mail.megacorpone.com
mail2.megacorpone.com
siem.megacorpone.com
support.megacorpone.com
...
```

Otra cosa a tener en cuenta es que dedicamos mucho tiempo y energía a reducir los resultados para encontrar y crear los nombres de los subdominios. Esto era torpe, propenso a errores y sin sentido cuando una búsqueda de expresión regular bien formada podía manejar esto fácilmente. En este ejemplo, usaremos una expresión regular simple para extraer los subdominios ".megacorpone.com" de nuestro archivo:

```
grep -o '[^/]*\.megacorpone\.com' index.html 
```
![6](https://user-images.githubusercontent.com/50930193/117070011-d1643300-acf2-11eb-8904-2599e0ac23a4.jpg)

Esta solución es bastante compacta, pero introduce algunas técnicas nuevas. Primero, observe la opción grep -o, que solo devuelve la cadena definida en nuestra expresión regular. Si formamos nuestra expresión con cuidado, este único comando manejará gran parte de nuestra talla de datos anterior. La cadena que estamos buscando ('[^/]*.megacorpone.com') está entre comillas simples, lo que, como mencionamos, no permitirá expansiones variables y tratará todos los caracteres encerrados literalmente.

El primer bloque de la expresión ([^/] ) es un conjunto negado (^) ([ ]), que busca cualquier número de caracteres ( ) sin incluir una barra diagonal. Tenga en cuenta que los puntos se escapan con una barra invertida (.) para reforzar que estamos buscando un punto literal. A continuación, la cadena debe terminar en “.megacorpone.com”. Cuando grep encuentra una cadena coincidente, la extrae de la línea y la devuelve. Podemos aprender más sobre expresiones regulares en

```
https://regexr.com/
```
Además, podemos redirigir a la salida a un archivo de texto
```
grep -o '[^/]*\.megacorpone\.com' index.html > subdomains.txt
cat subdomains.txt
```

![8](https://user-images.githubusercontent.com/50930193/117070661-adedb800-acf3-11eb-9733-557c1b247693.jpg)

Podemos usar el comando "host" para conocer la dirección IP. Extraeremos las direcciones IP canalizando la salida a un grep para "tiene dirección", luego cortaremos los resultados y los ordenaremos:

```
for url in $(cat subdomain.txt); do host $url; done
```
![9](https://user-images.githubusercontent.com/50930193/117071579-e5a92f80-acf4-11eb-93eb-dc842403d195.jpg)

¡Excelente! Hemos extraído los subdominios “.megacorpone.com” de la página web y obtenido las direcciones IP correspondientes. 

### Extracción de subdominios con RECON-NG

*recon-ng* es un marco basado en módulos para la recopilación de información basada en la web. Recon-ng muestra los resultados de un módulo en el terminal, pero también los almacena en una base de datos. Gran parte del poder de recon-ng radica en pasar los resultados de un módulo a otro, lo que nos permite ampliar rápidamente el alcance de nuestra recopilación de información. Usemos recon-ng para compilar datos interesantes sobre MegaCorp One. Para comenzar, simplemente ejecutemos recon-ng:

```
recon-ng
[*] Version check disabled.
/\

    _/_/_/    _/_/_/_/    _/_/_/    _/_/_/    _/      _/            _/      _/    _/_/_/
   _/    _/  _/        _/        _/      _/  _/_/    _/            _/_/    _/  _/       
  _/_/_/    _/_/_/    _/        _/      _/  _/  _/  _/  _/_/_/_/  _/  _/  _/  _/  _/_/_/
 _/    _/  _/        _/        _/      _/  _/    _/_/            _/    _/_/  _/      _/ 
_/    _/  _/_/_/_/    _/_/_/    _/_/_/    _/      _/            _/      _/    _/_/_/    


                                          /\
                                         / \\ /\
    Sponsored by...               /\  /\/  \\V  \/\
                                 / \\/ // \\\\\ \\ \/\
                                // // BLACK HILLS \/ \\
                               www.blackhillsinfosec.com

                  ____   ____   ____   ____ _____ _  ____   ____  ____
                 |____] | ___/ |____| |       |   | |____  |____ |
                 |      |   \_ |    | |____   |   |  ____| |____ |____
                                   www.practisec.com

                      [recon-ng v5.1.1, Tim Tomes (@lanmaster53)]                       

[*] No modules enabled/installed.

[recon-ng][default] >
```

Según el resultado, necesitamos instalar varios módulos para usar recon-ng. Podemos agregar módulos desde el “Marketplace” de reconocimiento. Buscaremos en el mercado desde el indicador principal con búsqueda en el mercado, proporcionando una cadena de búsqueda como argumento.

En este ejemplo, buscaremos módulos que contengan el término github:
```
[recon-ng][default] >  marketplace search github
[*] Searching module index for 'github'...

  +------------------------------------------------------------------------------------------------+
  |                       Path                      | Version |     Status    |  Updated   | D | K |
  +------------------------------------------------------------------------------------------------+
  | recon/companies-multi/github_miner              | 1.1     | not installed | 2020-05-15 |   | * |
  | recon/profiles-contacts/github_users            | 1.0     | not installed | 2019-06-24 |   | * |
  | recon/profiles-profiles/profiler                | 1.0     | not installed | 2019-06-24 |   |   |
  | recon/profiles-repositories/github_repos        | 1.1     | not installed | 2020-05-15 |   | * |
  | recon/repositories-profiles/github_commits      | 1.0     | not installed | 2019-06-24 |   | * |
  | recon/repositories-vulnerabilities/github_dorks | 1.0     | not installed | 2019-06-24 |   | * |
  +------------------------------------------------------------------------------------------------+

  D = Has dependencies. See info for details.
  K = Requires keys. See info for details.
[recon-ng][default] >
```

Observe que algunos de los módulos están marcados con un asterisco en la columna "K". Estos módulos requieren credenciales o API para proveedores externos. El recon-ng mantiene una lista corta de las claves utilizadas por sus módulos. Algunas de estas claves están disponibles para cuentas gratuitas, mientras que otras requieren una suscripción.

Podemos obtener más información sobre un módulo utilizando la información del mercado seguida del nombre del módulo.

Dado que los módulos de GitHub requieren claves API, usemos este comando para examinar el módulo netcraft:

```
[recon-ng][default] > marketplace info netcraft

  +-----------------------------------------------------------------------------------------------+
  | path          | recon/domains-hosts/netcraft                                                  |
  | name          | Netcraft Hostname Enumerator                                                  |
  | author        | thrapt (thrapt@gmail.com)                                                     |
  | version       | 1.1                                                                           |
  | last_updated  | 2020-02-05                                                                    |
  | description   | Harvests hosts from Netcraft.com. Updates the 'hosts' table with the results. |
  | required_keys | []                                                                            |
  | dependencies  | []                                                                            |
  | files         | []                                                                            |
  | status        | not installed                                                                 |
  +-----------------------------------------------------------------------------------------------+

[recon-ng][default] > 

```
Según su descripción, este módulo busca en Google con el operador "site" y no requiere una clave API. Instalemos el módulo con la instalación del mercado:
```
[recon-ng][default] > marketplace install recon/domains-hosts/netcraft 
[*] Module installed: recon/domains-hosts/netcraft
[*] Reloading modules...
[recon-ng][default] >
```
Luego de instalar el módulo, podemos cargarlo con module load seguido de su nombre. Luego, usaremos info para mostrar detalles sobre el módulo y los parámetros requeridos:
```
[recon-ng][default] > modules load recon/domains-hosts/netcraft
[recon-ng][default][netcraft] > info

      Name: Netcraft Hostname Enumerator
    Author: thrapt (thrapt@gmail.com)
   Version: 1.1

Description:
  Harvests hosts from Netcraft.com. Updates the 'hosts' table with the results.

Options:
  Name    Current Value  Required  Description
  ------  -------------  --------  -----------
  SOURCE  default        yes       source of input (see 'info' for details)

Source Options:
  default        SELECT DISTINCT domain FROM domains WHERE domain IS NOT NULL
  <string>       string representing a single input
  <path>         path to a file containing a list of inputs
  query <sql>    database query returning one column of inputs

[recon-ng][default][netcraft] > 

```

Observe que la salida contiene información adicional sobre el módulo ahora que lo hemos instalado y cargado. Según el resultado, el módulo requiere el uso de una fuente, que es el objetivo sobre el que queremos recopilar información.

En este caso, utilizaremos las opciones establecidas en SOURCE megacorpone.com para establecer nuestro dominio de destino:
```
[recon-ng][default][netcraft] > options set SOURCE megacorpone.com
SOURCE => megacorpone.com
[recon-ng][default][netcraft] > run

---------------
MEGACORPONE.COM
---------------
[*] URL: http://searchdns.netcraft.com/?restriction=site%2Bends%2Bwith&host=megacorpone.com
[*] Country: None
[*] Host: intranet.megacorpone.com
[*] Ip_Address: None
[*] Latitude: None
[*] Longitude: None
[*] Notes: None
[*] Region: None
[*] --------------------------------------------------
[*] Country: None
[*] Host: www.megacorpone.com
[*] Ip_Address: None
[*] Latitude: None
[*] Longitude: None
[*] Notes: None
[*] Region: None
[*] --------------------------------------------------
[*] Country: None
[*] Host: support.megacorpone.com
[*] Ip_Address: None
[*] Latitude: None
[*] Longitude: None
[*] Notes: None
[*] Region: None
[*] --------------------------------------------------
[*] Country: None
[*] Host: siem.megacorpone.com
[*] Ip_Address: None
[*] Latitude: None
[*] Longitude: None
[*] Notes: None
[*] Region: None
[*] --------------------------------------------------

-------
SUMMARY
-------
[*] 4 total (3 new) hosts found.
[recon-ng][default][netcraft] > 

```
Los resultados reflejan lo que encontramos en la búsqueda de DNS de Netcraft. Sin embargo, no hemos perdido nuestro tiempo aquí. Recon-ng almacena los resultados en una base de datos local y estos resultados se incorporarán a otros módulos de reconocimiento. Podemos usar el comando show hosts para ver los datos almacenados:

```
[recon-ng][default][netcraft] > back
[recon-ng][default] > show
Shows various framework items

Usage: show <companies|contacts|credentials|domains|hosts|leaks|locations|netblocks|ports|profiles|pushpins|repositories|vulnerabilities>

[recon-ng][default] > show domains
[*] No data returned.
[recon-ng][default] > show hosts

  +-------------------------------------------------------------------------------------------------------------------+
  | rowid |           host           | ip_address | region | country | latitude | longitude | notes |      module     |
  +-------------------------------------------------------------------------------------------------------------------+
  | 1     | www.megacorpone.com      |            |        |         |          |           |       | google_site_web |
  | 2     | intranet.megacorpone.com |            |        |         |          |           |       | netcraft        |
  | 3     | support.megacorpone.com  |            |        |         |          |           |       | netcraft        |
  | 4     | siem.megacorpone.com     |            |        |         |          |           |       | netcraft        |
  +-------------------------------------------------------------------------------------------------------------------+

[*] 4 rows returned
[recon-ng][default] > 
```

Tenemos cuatro hosts en nuestra base de datos pero no hay información adicional sobre ellos. Quizás otro módulo pueda completar las direcciones IP. Examinemos recon/hosts-hosts/resolve con información del mercado:
```

[recon-ng][default] > marketplace info resolve

  +-------------------------------------------------------------------------------------------------+
  | path          | recon/hosts-hosts/resolve                                                       |
  | name          | Hostname Resolver                                                               |
  | author        | Tim Tomes (@lanmaster53)                                                        |
  | version       | 1.0                                                                             |
  | last_updated  | 2019-06-24                                                                      |
  | description   | Resolves the IP address for a host. Updates the 'hosts' table with the results. |
  | required_keys | []                                                                              |
  | dependencies  | []                                                                              |
  | files         | []                                                                              |
  | status        | not installed                                                                   |
  +-------------------------------------------------------------------------------------------------+
```
La descripción del módulo se adapta a nuestras necesidades, por lo que lo instalaremos con la instalación del mercado:
```
[recon-ng][default] > marketplace install recon/hosts-hosts/resolve
[*] Module installed: recon/hosts-hosts/resolve
[*] Reloading modules...
[recon-ng][default] > 
```
Una vez que el módulo está instalado, podemos usarlo con la carga de módulos y ejecutar info para mostrar información sobre el módulo y sus opciones:
```
[recon-ng][default] > modules load recon/hosts-hosts/resolve
[recon-ng][default][resolve] > info

      Name: Hostname Resolver
    Author: Tim Tomes (@lanmaster53)
   Version: 1.0

Description:
  Resolves the IP address for a host. Updates the 'hosts' table with the results.

Options:
  Name    Current Value  Required  Description
  ------  -------------  --------  -----------
  SOURCE  default        yes       source of input (see 'info' for details)

Source Options:
  default        SELECT DISTINCT host FROM hosts WHERE host IS NOT NULL AND ip_address IS NULL
  <string>       string representing a single input
  <path>         path to a file containing a list of inputs
  query <sql>    database query returning one column of inputs

Comments:
  * Note: Nameserver must be in IP form.

[recon-ng][default][resolve] > 
```
Como se desprende claramente del resultado anterior, este módulo resolverá la dirección IP de un host. Necesitamos proporcionar la dirección IP que queremos resolver como nuestra fuente. Tenemos cuatro opciones que podemos configurar para la fuente: predeterminada, cadena, ruta y consulta. Cada opción tiene una descripción a su lado, como se muestra en el Listado 206. Por ejemplo, en el módulo de reconocimiento "netcraft", usamos un valor de cadena.

Sin embargo, esta vez queremos aprovechar la base de datos. Si usamos el valor "default", recon-ng buscará la información del host en su base de datos para cualquier registro que tenga un nombre de host pero no una dirección IP.

Como se muestra, tenemos cuatro hosts sin direcciones IP. Si seleccionamos una fuente "default", el módulo se ejecutará automáticamente en los cuatro hosts de nuestra base de datos. Probemos esto dejando nuestra fuente establecida en "default" y luego ejecutemos el módulo:
```
[recon-ng][default][resolve] > run
[*] www.megacorpone.com => 149.56.244.87
[*] intranet.megacorpone.com => 51.222.169.211
[*] support.megacorpone.com => 51.222.169.218
[*] siem.megacorpone.com => 51.222.169.215
```
Excelente. Ahora tenemos direcciones IP para los cuatro dominios.

Si volvemos a mostrar hosts, podemos comprobar que la base de datos se ha actualizado con los resultados de ambos módulos:
```
[recon-ng][default][resolve] > show hosts

  +-----------------------------------------------------------------------------------------------------------------------+
  | rowid |           host           |   ip_address   | region | country | latitude | longitude | notes |      module     |
  +-----------------------------------------------------------------------------------------------------------------------+
  | 1     | www.megacorpone.com      | 149.56.244.87  |        |         |          |           |       | google_site_web |
  | 2     | intranet.megacorpone.com | 51.222.169.211 |        |         |          |           |       | netcraft        |
  | 3     | support.megacorpone.com  | 51.222.169.218 |        |         |          |           |       | netcraft        |
  | 4     | siem.megacorpone.com     | 51.222.169.215 |        |         |          |           |       | netcraft        |
  +-----------------------------------------------------------------------------------------------------------------------+

[*] 4 rows returned
```
### Instalación de Golang

```
apt install libpcap-dev golang -y

```
### Instalación de httpx
```
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
```

### Instalación de assetfinder
```
go install github.com/tomnomnom/assetfinder@latest
```

### Instalación de subfinder
```
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
```

### Extracción de subdominios con Subscraper (Github Tool)

SubScraper es una herramienta de enumeración de subdominios que utiliza una variedad de técnicas para encontrar subdominios potenciales de un objetivo determinado. Esto es especialmente útil durante las pruebas de penetración o la caza de recompensas de errores para descubrir superficies de ataque adicionales. Dependiendo de los argumentos de CMD utilizados, SubScraper puede realizar búsquedas de DNS y solicitudes HTTP/S durante el proceso de enumeración para ayudar a priorizar los objetivos y ayudar en los próximos pasos potenciales.

Podemos instalar subscraper usando estos comandos
```
git clone https://github.com/m8r0wn/subscraper
cd subscraper
python3 setup.py install
```
Ejecute el comando para encontrar subdominios

```
subscraper www.megacorpone.com                                                      

      ____        _    ____                                 
     / ___| _   _| |__/ ___|  ___ _ __ __ _ _ __   ___ _ __ 
     \___ \| | | | '_ \___ \ / __| '__/ _` | '_ \ / _ \ '__|
      ___) | |_| | |_) |__) | (__| | | (_| | |_) |  __/ |   
     |____/ \__,_|_.__/____/ \___|_|  \__,_| .__/ \___|_|   
                                           |_|            v2.2.1 

[Source]             Subdomain                               	
[Archive.org]       www.megacorpone.com
[Archive.org]       www2.megacorpone.com
[Archive.org]       mail.megacorpone.com
[Archive.org]       siem.megacorpone.com
[Archive.org]       support.megacorpone.com
[Archive.org]       vpn.megacorpone.com

[*] Identified 6 subdomain(s) in 0:00:26.289842
[*] Subdomains written to ./subscraper_report.txt
```
Este comando permite el uso de parámetros para las enumeraciones, podemos agregar el nivel de enumeración, podemos ver los resultados.
```
  -e LVL, --enum LVL   Enumeration Level:
                       1: Subdomain Only (Default)
                       2: Live subdomains, verified by DNS
                       3: Live check & get HTTP/S response codes
```
Podemos usar este comando con resolución DNS o haciendo una verificación en vivo para cada subdominio
```
subscraper www.megacorpone.com -e 2

      ____        _    ____                                 
     / ___| _   _| |__/ ___|  ___ _ __ __ _ _ __   ___ _ __ 
     \___ \| | | | '_ \___ \ / __| '__/ _` | '_ \ / _ \ '__|
      ___) | |_| | |_) |__) | (__| | | (_| | |_) |  __/ |   
     |____/ \__,_|_.__/____/ \___|_|  \__,_| .__/ \___|_|   
                                           |_|            v2.2.1 

[Source]             Subdomain                               	DNS Resolution
[Archive.org]       www.megacorpone.com                     	['149.56.244.87']
[Archive.org]       www2.megacorpone.com                    	['149.56.244.87']
[Archive.org]       mail.megacorpone.com                    	['51.222.169.212']
[Archive.org]       siem.megacorpone.com                    	['51.222.169.215']
[Archive.org]       support.megacorpone.com                 	['51.222.169.218']
[Archive.org]       vpn.megacorpone.com                     	['51.222.169.220']

```
```
subscraper www.megacorpone.com -e 3

      ____        _    ____                                 
     / ___| _   _| |__/ ___|  ___ _ __ __ _ _ __   ___ _ __ 
     \___ \| | | | '_ \___ \ / __| '__/ _` | '_ \ / _ \ '__|
      ___) | |_| | |_) |__) | (__| | | (_| | |_) |  __/ |   
     |____/ \__,_|_.__/____/ \___|_|  \__,_| .__/ \___|_|   
                                           |_|            v2.2.1 

[Source]             Subdomain                               	(http/https)	DNS Resolution
[DNS-Dumpster]      www.megacorpone.com                     	(200/200)	['149.56.244.87']
[Archive.org]       www2.megacorpone.com                    	(200/200)	['149.56.244.87']
[Archive.org]       mail.megacorpone.com                    	(Err/Err)	['51.222.169.212']
[Archive.org]       siem.megacorpone.com                    	(Err/Err)	['51.222.169.215']
[Archive.org]       vpn.megacorpone.com                     	(Err/Err)	['51.222.169.220']
[Archive.org]       support.megacorpone.com                 	(Err/Err)	['51.222.169.218']
```
### Extracción de subdominios con dnsenum (From Kali)

Dnsenum es un script de herramienta de enumeración de subdominios para enumerar la información de DNS de un dominio y descubrir bloques de IP no contiguos.

Podemos ejecutar este comando
```
dnsenum -noreverse megacorpone.com
```
Obtenemos los resultados
```
-----   megacorpone.com   -----


Host's addresses:
__________________



Name Servers:
______________

ns3.megacorpone.com.                     5        IN    A        66.70.207.180
ns1.megacorpone.com.                     5        IN    A        51.79.37.18
ns2.megacorpone.com.                     5        IN    A        51.222.39.63


Mail (MX) Servers:
___________________

errdomain.                               5        IN    A         127.0.0.1


Trying Zone Transfers and getting Bind Versions:
_________________________________________________


Trying Zone Transfer for megacorpone.com on ns1.megacorpone.com ... 
AXFR record query failed: REFUSED

Trying Zone Transfer for megacorpone.com on ns2.megacorpone.com ... 
megacorpone.com.                         300      IN    SOA               (
megacorpone.com.                         300      IN    TXT            "Try
megacorpone.com.                         300      IN    TXT               (
megacorpone.com.                         300      IN    MX               10
megacorpone.com.                         300      IN    MX               20
megacorpone.com.                         300      IN    MX               50
megacorpone.com.                         300      IN    MX               60
megacorpone.com.                         300      IN    NS       ns1.megacorpone.com.
megacorpone.com.                         300      IN    NS       ns2.megacorpone.com.
megacorpone.com.                         300      IN    NS       ns3.megacorpone.com.
admin.megacorpone.com.                   300      IN    A        51.222.169.208
beta.megacorpone.com.                    300      IN    A        51.222.169.209
fs1.megacorpone.com.                     300      IN    A        51.222.169.210
intranet.megacorpone.com.                300      IN    A        51.222.169.211
mail.megacorpone.com.                    300      IN    A        51.222.169.212
mail2.megacorpone.com.                   300      IN    A        51.222.169.213
ns1.megacorpone.com.                     300      IN    A        51.79.37.18
ns2.megacorpone.com.                     300      IN    A        51.222.39.63
ns3.megacorpone.com.                     300      IN    A        66.70.207.180
router.megacorpone.com.                  300      IN    A        51.222.169.214
siem.megacorpone.com.                    300      IN    A        51.222.169.215
snmp.megacorpone.com.                    300      IN    A        51.222.169.216
support.megacorpone.com.                 300      IN    A        51.222.169.218
syslog.megacorpone.com.                  300      IN    A        51.222.169.217
test.megacorpone.com.                    300      IN    A        51.222.169.219
vpn.megacorpone.com.                     300      IN    A        51.222.169.220
www.megacorpone.com.                     300      IN    A        149.56.244.87
www2.megacorpone.com.                    300      IN    A        149.56.244.87

Trying Zone Transfer for megacorpone.com on ns3.megacorpone.com ... 
AXFR record query failed: REFUSED


Brute forcing with /usr/share/dnsenum/dns.txt:
_______________________________________________



megacorpone.com class C netranges:
___________________________________

 51.79.37.0/24
 51.222.39.0/24
 51.222.169.0/24
 66.70.207.0/24
 149.56.244.0/24


megacorpone.com ip blocks:
___________________________

 51.79.37.18/32
 51.222.39.63/32
 51.222.169.208/29
 51.222.169.216/30
 51.222.169.220/32
 66.70.207.180/32
 149.56.244.87/32

done.

```
### Extracción de subdominios con Photon (Github Tool)

Photon es una herramienta de rastreo, Photon puede extraer las siguientes URL de datos (dentro y fuera del alcance), URL con parámetros (example.com/gallery.php?id=2), Intel (correos electrónicos, cuentas de redes sociales, cubos de amazon, etc.), archivos (pdf, png, xml, etc.), claves secretas (auth/claves API y hashes), archivos JavaScript y puntos finales presentes en ellos, cadenas que coinciden con patrones de expresiones regulares personalizados, subdominios y datos relacionados con DNS.

Podemos instalar subscraper usando estos comandos
```
git clone https://github.com/s0md3v/Photon.git
cd Photon
chmod +x photon.py
```
Ejecute el comando para encontrar subdominios

```
python3 photon.py --url www.megacorpone.com --dns
```
Obtenemos los resultados
```

      ____  __          __
     / __ \/ /_  ____  / /_____  ____
    / /_/ / __ \/ __ \/ __/ __ \/ __ \
   / ____/ / / / /_/ / /_/ /_/ / / / /
  /_/   /_/ /_/\____/\__/\____/_/ /_/ v1.3.2

[+] URLs retrieved from robots.txt: 2
[~] Level 1: 3 URLs
[!] Progress: 3/3
[~] Level 2: 5 URLs
[!] Progress: 5/5
[~] Crawling 8 JavaScript files
[!] Progress: 8/8
--------------------------------------------------
[+] Intel: 20
[+] Robots: 2
[+] Internal: 12
[+] Scripts: 8
[+] External: 27
--------------------------------------------------
[!] Total requests made: 17
[!] Total time taken: 0 minutes 8 seconds
[!] Requests per second: 2
[~] Enumerating subdomains
[!] 0 subdomains found
[~] Generating DNS map
[+] Results saved in www.megacorpone.com directory
           
```

Also we can browse in the ouput directoy and we can open the megacorpone.com.png  file

![10](https://user-images.githubusercontent.com/50930193/117081220-185b2400-ad05-11eb-8f5a-bbb06259fa44.jpg)



### Extracción de subdominios con otras herramientas  (Kali Tools and Github Tool)

Instalar y ejecutar SUBLIST3R
```
https://github.com/aboul3la/Sublist3r.git
cd Sublist3r
python3 sublist3r.py -d megacorpone.com
```

Desde Kali sin instalar nada ejecutar dmitry
```
dmitry megacorpone.com
```

Desde Kali sin instalar nada ejecutar fierce
```
fierce --domain megacorpone.com
```
Desde Kali sin instalar nada ejecutar dig
```
dig –t MX megacorpone.com
```
