[WriteUp Doorkick 3cb4ba782792804280dadaa8928bb304.md](https://github.com/user-attachments/files/31761515/WriteUp.Doorkick.3cb4ba782792804280dadaa8928bb304.md)[Uploading WriteUp Doorkick 3c# WriteUp Doorkick

<img width="1029" height="680" alt="image" src="https://github.com/user-attachments/assets/c7941918-1c0c-4f51-95db-275539867f9d" />


> Descubrimiento de puertos con NMAP
> 

<img width="958" height="255" alt="image" src="https://github.com/user-attachments/assets/aa9c5a66-0d90-4cfa-922d-01f96f02f98e" />

Como podemos ver tenemos un servicio en el puerto 5000.

> Verificamos servicio con el puerto 5000 (HTTP)
> 

<img width="1495" height="888" alt="image" src="https://github.com/user-attachments/assets/5da8bbda-2d3a-4163-bffb-ec9ae607540d" />


Podemos observar tenemos un login bastante simple, lo primero sera explorar un poco que podemos realizar con el login, al parecer tenemos un inicio de sesion que a simple vista no acepta claves comunes como “admin” o “admin123” y exploraremos la pista.

<img width="1112" height="445" alt="image" src="https://github.com/user-attachments/assets/14f459a7-f877-432d-afcd-60a7e60ce5a3" />


Al parecer tenemos un nonce que probablemente se utilice para explotar cualquier permiso o inicio de sesion con los encabeados en HTTP, vamos a guardar la pista y despues trataremos de explotar el nonce con burnsuite para observar si se puede predecir cualquier otro roken de session.

<img width="1495" height="888" alt="image" src="https://github.com/user-attachments/assets/b4c8212c-7bcf-48b6-a5b4-1502cbcfde4f" />

Verificamos el codigo fuente y no encontramos anomalias

> Primero hacemos un analisis de direcciones para mitigar cabos sueltos
> 

<img width="1065" height="711" alt="image" src="https://github.com/user-attachments/assets/4ac65f28-0ac3-48e1-ad94-5474f3d31207" />

> Abrimos burnsuite
>

<img width="1637" height="903" alt="image" src="https://github.com/user-attachments/assets/347e7c32-831f-462c-ad82-eea1a7eeb82e" />

Tal como nos indica la pista “Use for Sequencer Manual load” vamos a intentar utilizar la herramienta “sequencer” dentro de burnsuite para predecir algun token de session,

> Analisis de encabezados en Proxy
> 

<img width="1637" height="903" alt="image" src="https://github.com/user-attachments/assets/e77c1d64-5550-4790-abb8-4bf6efa25201" />


Al parecer no encontramos ningun encabezado que contenga “Cookies” o “Session”, por lo que el siguiente paso seria intentar predecir el encabezado al cual se le esta haciendo uso.

> Mandamos la petición desde el proxy hacia el sequencer
> 

<img width="1637" height="903" alt="image" src="https://github.com/user-attachments/assets/bf3f41ec-1e06-4821-8b96-413a79a2b4c1" />

> Vamos a analizar que tan débil es la construcción del nonce.
>

<img width="1637" height="903" alt="image" src="https://github.com/user-attachments/assets/4f158b1d-ad06-4d23-bb41-16771ae2c132" />

> Ahora usamos Start live capture
> 

<img width="1637" height="903" alt="image" src="https://github.com/user-attachments/assets/5e58102d-8f26-4013-a7dc-c27391ac700f" />


> Análisis del Resultado del Sequencer
> 

<img width="1007" height="926" alt="image" src="https://github.com/user-attachments/assets/57cc9cc6-45a0-4a9c-9bf1-0199cee1491a" />
<img width="1007" height="926" alt="image" src="https://github.com/user-attachments/assets/a3267337-a4eb-403b-b5b8-f7b1909f87b1" />

Los valores arrojados nos indican que el token “nonce” es bastante debil y facil de explotar. 

> NOTA: antes de iniciar un ataque con nonce no hemos verificado algo mas importante y es, un ataque de fuerza bruta, por lo que es necesario que en caso de que la fuerza bruta no funcione, intentemos inyectar el encabezado de la cookie con nonce.
> 

> Probamos un ataque de fuerza bruta con hydra.
> 

<img width="1798" height="366" alt="image" src="https://github.com/user-attachments/assets/1a641b13-9dbe-4322-bac6-9b29afbd32d3" />

Nota: No funciono con hydra así que intentaremos con otro.

> Segundo intento de fuerza bruta con wfuzz.
> 

<img width="1284" height="495" alt="image" src="https://github.com/user-attachments/assets/b83b5a5e-2bdd-418c-a4ff-a779b19c61a9" />

Como podemos observar tenemos una coincidencia 

> Respuesta de laboratorio
>

<img width="1322" height="801" alt="image" src="https://github.com/user-attachments/assets/5c3dfb8e-bfe0-4d50-80ca-b3390560bbca" />

Al parecer me precipite un poco sin embargo, es importante no dejar ningún cabo suelto antes de querer realizar cualquier otro tipo de ataque.b4ba782792804280dadaa8928bb304.md…]()
