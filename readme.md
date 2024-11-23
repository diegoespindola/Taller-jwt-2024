# Taller de Vulnerabilidades en jwt

Un taller mas sobre vulnerabilidades en JWT, este es practico, paso a paso y en español

## Laboratorio

Para realizar el taller paso a paso debemos tener lo siguiente:

### Web

 Necesitamos una cuenta activa en la plataforma de [https://portswigger.net/](https://portswigger.net/users/register)

### Herramientas

Las siguientes herramientas deben estar instaladas

#### CAIDO

Debemos crear una cuenta en <https://caido.io/> y descargar el instalador para nuestro sistema operativo

#### GIT

Debemos instalar git segun las siguientes [instrucciones](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
#### Python3

Instaslaremos Python 3 segun las siguientes [instrucciones](https://www.python.org/downloads/)
#### JWT-TOOL 

Instalaremos [JWT_TooL](https://github.com/ticarpi/jwt_tool)

`git clone https://github.com/ticarpi/jwt_tool`

`python3 -m pip install -r requirements.txt`

## Vulnerabilidades

### Key confusion attack

#### Taller:

 Usaremos el siguiente [Laboratorio de portswigger](https://portswigger.net/web-security/jwt/algorithm-confusion/lab-jwt-authentication-bypass-via-algorithm-confusion)

- Abrimos la url del laboratorio con CAIDO
	- Imgresamos con las credenciales wiener:peter
- Buscamos el token entregado por el servidor, vemos que usa KID si tenemos suerte  deberia haber un archivo con la clave publica con ese id.
- usamos dirb para ver si hay algun archivo esta expuesto con las claves publicas 

	`dirb https://lab1.web-security-academy.net/ ~/tools/diccionarios/SecLists/Discovery/Web-Content/common.txt 
`- Con lo anterior descubrimos un archivo https://lab1.web-security-academy.net/jwks.json


