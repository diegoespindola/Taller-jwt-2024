# Taller de Vulnerabilidades en jwt

Un taller mas sobre vulnerabilidades en JWT, este es practico, paso a paso y en español.

Toda la teoria y conocimientos previos van a ser explicados en la charla en vivo. sera agregada luego.

## Laboratorio

Para realizar el taller paso a paso debemos tener lo siguiente:

### Webs

 - Necesitamos una cuenta activa en la plataforma de [https://portswigger.net/](https://portswigger.net/users/register)
 - Usaremos https://token.dev/ para revisar los jwt.
 - Para transformar los certificados usaremos <https://8gwifi.org/jwkconvertfunctions.jsp>

### Diccionarios
Usaremos un archivo de la coleccion  [SecLists](https://github.com/danielmiessler/SecLists) de Daniel Miessler.

`git clone https://github.com/danielmiessler/SecLists.git`


### Herramientas

Las siguientes herramientas deben estar instaladas

- #### CAIDO

	Debemos crear una cuenta en <https://caido.io/> y descargar el instalador para nuestro sistema operativo

- #### GIT

	Debemos instalar git segun las siguientes [instrucciones](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

- #### Python3

	Instalaremos Python 3 segun las siguientes [instrucciones](https://www.python.org/downloads/)

- #### Ffuf
	instalaremos Ffuf segun las instrucciones en la url <https://github.com/ffuf/ffuf>


- #### JWT-TOOL 

	Instalaremos [JWT_TooL](https://github.com/ticarpi/jwt_tool)

	`git clone https://github.com/ticarpi/jwt_tool`

	`python3 -m pip install -r requirements.txt`

## Vulnerabilidades

### No valida firma

Este ataque aprovecha que algunas implementaciones de librerias de JWT tienen distintos metodos para validar el token y obtener los claims, entonces algunos desarroladores obtienen los claims antes de validar la firma del token.

[Laboratorio](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature)

### alg none

Esta vulnerabilidad se aprovecha que algunas implementaciones utilizan siempre el algoritmo indicado en la cabecera para validar el token. 

[Laboratorio](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification)

### Claves debiles

Un token con una clave debil puede ser facilmente falsificado, obteniendo la clave por fuerza bruta.

[Laboratorio](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-weak-signing-key)

### jwk injection

Inyectaremos la clave para validar el token directamente en el header del token

[Laboratorio](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jwk-header-injection)


### Key confusion attack

Este ataque se aprovecha que algunas librerias usan siempre el algoritmo que viene en el Header par validar los tokens, entonces se cambia el algoritmo de cifrado desde uno asincronico como el RS256 a uno sincronico como es el HS256, basicamente el sistema deberia validar el token con la clave publica siempre, entonces firmamos el jwt con el algoritmo sincronico y usamos la misma clave publica (de validacion) para firmar el token.

#### Taller:

 Usaremos el siguiente [Laboratorio de portswigger](https://portswigger.net/web-security/jwt/algorithm-confusion/lab-jwt-authentication-bypass-via-algorithm-confusion)

- Abrimos la url del laboratorio con CAIDO
	- Ingresamos con las credenciales wiener:peter
- Buscamos el token entregado por el servidor, vemos que usa KID si tenemos suerte  deberia haber un archivo con la clave publica con ese id.
- Usamos dirb para ver si hay algun archivo esta expuesto con las claves publicas 

	`ffuf -u https://lab1.web-security-academy.net/FUZZ -w ~/tools/diccionarios/SecLists/Discovery/Web-Content/common.txt -mc 200`

- Con lo anterior descubrimos un archivo https://lab1.web-security-academy.net/jwks.json

- Transformamos el jwk a formato pem en <https://8gwifi.org/jwkconvertfunctions.jsp>

- Usamos el PEM para firmar nuestro tokenmodificado en <https://token.dev/> 
*Base64 encoded off*
	- Pegamos nuestro token en la seccion JWT String
	- Modificamos el Header, en el campo alg reemplazamos el valor RS256 por HS256
	- Modificamos el Payload, en el campo sub reemplazamos el valor "wiener" por "administrator".
	- Pegamos el texto del archivo PEM en el campo Signing Key *Base64 encoded off*.
	- Y obtenemos nuestro token firmado.

- Para usar nuestro nuevo token primero enviaremos en CAIDO a Replay el request de "/my-account"
	- Reemplazamos el valor del parametro id, en la url, en vez de "wiener" va "administrator".
	- Reemplazamos el token por el nuestro.
	- Le damos a Send.
	- si encontramos en el response el texto "Your username is: administrator"entonces nuestro tokenfunciono ok.

- Para usar el token y resolver el laboratorios creamos 2 reglas en "match & replace" de CAIDO, donde reemplazaremos automaticamente el token real por el nuestro y reemplazaremos automaticamente el nombre de usuario de "wiener" por "administrator" y luego navegamos desde el chrome de CAIDO.