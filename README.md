# WriteUp: Doorkick

---

## 1. Reconocimiento Inicial y Descubrimiento de Puertos

Efectuamos un escaneo con **Nmap** para identificar puertos abiertos y servicios activos en la máquina objetivo.

![Descubrimiento de puertos con NMAP](https://github.com/user-attachments/assets/c7941918-1c0c-4f51-95db-275539867f9d)

![Resultado del escaneo Nmap](https://github.com/user-attachments/assets/aa9c5a66-0d90-4cfa-922d-01f96f02f98e)

> **Observación:** Como podemos ver tenemos un servicio en el puerto `5000`.

---

## 2. Inspección del Servicio HTTP (Puerto 5000)

Verificamos el servicio corriendo en el puerto 5000 a través del navegador web.

![Interfaz web en puerto 5000](https://github.com/user-attachments/assets/5da8bbda-2d3a-4163-bffb-ec9ae607540d)

Podemos observar que tenemos un login bastante simple. Lo primero será explorar un poco qué podemos realizar con el login. Al parecer tenemos un inicio de sesión que a simple vista no acepta claves comunes como `admin` o `admin123`, por lo que exploraremos la pista disponible.

![Pista obtenida en la interfaz](https://github.com/user-attachments/assets/1112445-placeholder) *(Pista del nonce)*

![Analizando pista del nonce](https://github.com/user-attachments/assets/14f459a7-f877-432d-afcd-60a7e60ce5a3)

Al parecer tenemos un nonce que probablemente se utilice para explotar cualquier permiso o inicio de sesión con los encabezados en HTTP. Vamos a guardar la pista y después trataremos de explotar el nonce con **Burp Suite** para observar si se puede predecir cualquier otro token de sesión.

![Revisión del código fuente](https://github.com/user-attachments/assets/1495888-placeholder)

Verificamos el código fuente y no encontramos anomalías.

---

## 3. Enumeración de Directorios y Análisis con Burp Suite

> Primero hacemos un análisis de direcciones para mitigar cabos sueltos.

![Fuzzing de directorios](https://github.com/user-attachments/assets/4ac65f28-0ac3-48e1-ad94-5474f3d31207)

Abrimos Burp Suite para interceptar las peticiones.

![Interfaz de Burp Suite](https://github.com/user-attachments/assets/347e7c32-831f-462c-ad82-eea1a7eeb82e)

Tal como nos indica la pista *"Use for Sequencer Manual load"*, vamos a intentar utilizar la herramienta **Sequencer** dentro de Burp Suite para predecir algún token de sesión.

### Análisis de Encabezados en el Proxy

![Análisis de cabeceras en Proxy](https://github.com/user-attachments/assets/e77c1d64-5550-4790-abb8-4bf6efa25201)

Al parecer no encontramos ningún encabezado que contenga `Cookies` o `Session`, por lo que el siguiente paso sería intentar predecir el encabezado al cual se le está haciendo uso.

---

## 4. Análisis de Aleatoriedad con Burp Sequencer

Mandamos la petición desde el proxy hacia el Sequencer:

![Enviando petición a Sequencer](https://github.com/user-attachments/assets/bf3f41ec-1e06-4821-8b96-413a79a2b4c1)

Vamos a analizar qué tan débil es la construcción del nonce:

![Configuración del Sequencer](https://github.com/user-attachments/assets/4f158b1d-ad06-4d23-bb41-16771ae2c132)

Ahora usamos **Start live capture**:

![Captura de muestras en vivo](https://github.com/user-attachments/assets/5e58102d-8f26-4013-a7dc-c27391ac700f)

### Análisis del Resultado del Sequencer

![Resultado de entropía Sequencer 1](https://github.com/user-attachments/assets/57cc9cc6-45a0-4a9c-9bf1-0199cee1491a)
![Resultado de entropía Sequencer 2](https://github.com/user-attachments/assets/a3267337-a4eb-403b-b5b8-f7b1909f87b1)

Los valores arrojados nos indican que el token `nonce` es bastante débil y fácil de explotar.

> [!NOTE]
> **NOTA:** Antes de iniciar un ataque con nonce no hemos verificado algo más importante y es un ataque de fuerza bruta, por lo que es necesario que en caso de que la fuerza bruta no funcione, intentemos inyectar el encabezado de la cookie con nonce.

---

## 5. Ataque de Fuerza Bruta y Resolución

Probamos un ataque de fuerza bruta con **Hydra**:

![Prueba con Hydra](https://github.com/user-attachments/assets/1798366-placeholder)

> **Nota:** No funcionó con Hydra así que intentaremos con otro.

Segundo intento de fuerza bruta con **Wfuzz**:

![Resultado exitoso con Wfuzz](https://github.com/user-attachments/assets/b83b5a5e-2bdd-418c-a4ff-a779b19c61a9)

Como podemos observar, tenemos una coincidencia.

---

## 6. Respuesta del Laboratorio

![Respuesta y bandera final](https://github.com/user-attachments/assets/5c3dfb8e-bfe0-4d50-80ca-b3390560bbca)

Al parecer me precipité un poco; sin embargo, es importante no dejar ningún cabo suelto antes de querer realizar cualquier otro tipo de ataque.
