<p align="center">
  <img src="https://i.postimg.cc/Xv2nKS46/banner.png" alt="Formulario" width=100%/>
</p>

# Embedded-PaymentForm-PHP

## Índice

➡️ [1. Introducción](#1-introducción)  
🔑 [2. Requisitos previos](#2-requisitos-previos)  
🚀 [3. Ejecutar ejemplo](#3-despliegue)  
🔗 [4. Pasos de integración](#4-datos-de-conexión)  
💻 [Paso 1: Desplegar pasarela](#4-datos-de-conexión)  
💳 [Paso 2: Analizar resultado de pago](#5-transacción-de-prueba)  
📡 [Paso 3: Pase a producción](#6-implementación-de-la-ipn)  
🎨 [5. Personalización](#7-personalización)  
📚 [6. Consideraciones](#8-consideraciones)

## ➡️ 1. Introducción

En este manual podrás encontrar una guía paso a paso para configurar un proyecto de **[PHP]** con la pasarela de pagos de IZIPAY. Te proporcionaremos instrucciones detalladas y credenciales de prueba para la instalación y configuración del proyecto, permitiéndote trabajar y experimentar de manera segura en tu propio entorno local.
Este manual está diseñado para ayudarte a comprender el flujo de la integración de la pasarela para ayudarte a aprovechar al máximo tu proyecto y facilitar tu experiencia de desarrollo.

> [!IMPORTANT]
> En la última actualización se agregaron los campos: **nombre del tarjetahabiente** y **correo electrónico** (Este último campo se visualizará solo si el dato no se envía en la creación del formtoken).

<p align="center">
  <img src="https://github.com/izipay-pe/Imagenes/blob/main/formulario_incrustado/Imagen-Formulario-Incrustado.png" alt="Formulario" width="350"/>
</p>

## 🔑 2. Requisitos Previos

- Comprender el flujo de comunicación de la pasarela. [Información Aquí](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- Extraer credenciales del Back Office Vendedor. [Guía Aquí](https://github.com/izipay-pe/obtener-credenciales-de-conexion)
- Para este proyecto utilizamos la herramienta Visual Studio Code.
- Servidor Web
- PHP 7.0 o superior
> [!NOTE]
> Tener en cuenta que, para que el desarrollo de tu proyecto, eres libre de emplear tus herramientas preferidas.

## 🚀 3. Ejecutar ejemplo

### Instalar Xampp u otro servidor local compatible con php

Xampp, servidor web local multiplataforma que contiene los intérpretes para los lenguajes de script de php. Para instalarlo:

1. Dirigirse a la página web de [xampp](https://www.apachefriends.org/es/index.html)
2. Descargarlo e instalarlo.
3. Inicia los servicios de Apache desde el panel de control de XAMPP.


### Clonar el proyecto
```sh
git clone https://github.com/izipay-pe/Embedded-PaymentForm-Php.git
``` 

### Datos de conexión 

**Nota**: Reemplace **[CHANGE_ME]** con sus credenciales de `API REST` extraídas desde el Back Office Vendedor, ver [Requisitos Previos](#Requisitos_Previos).

- Editar en `keys.example.php` en la ruta raiz del proyecto:
```php
// Identificador de su tienda
define("USERNAME", "~ CHANGE_ME_USER_ID ~");

// Clave de Test o Producción
define("PASSWORD", "~ CHANGE_ME_PASSWORD ~");

// Clave Pública de Test o Producción
define("PUBLIC_KEY","~ CHANGE_ME_PUBLIC_KEY ~");

// Clave HMAC-SHA-256 de Test o Producción
define("HMAC_SHA256","~ CHANGE_ME_HMAC_SHA_256 ~");
```

### Ejecutar proyecto

1. Mover el proyecto y descomprimirlo en la carpeta htdocs en la ruta de instalación de Xampp: `C://xampp/htdocs/[proyecto_php]`

2.  Abrir el navegador web(Chrome, Mozilla, Safari, etc) con el puerto 80 que abrió xampp : `http://localhost:80/[nombre_de_proyecto]` y realizar una compra de prueba.


## 4. Pasos de integración

<p align="center">
  <img src="https://i.postimg.cc/pT6SRjxZ/3-pasos.png" alt="Formulario" />
</p>

## 1️⃣: Desplegar pasarela
#### Autentificación
Extraer las claves del Backoffice, concatenar `usuario:contraseña` y encriptarlo en base64
```php
$auth = $this->_username . ":" . $this->_password;
...
curl_setopt($curl, CURLOPT_USERPWD, $auth);
curl_setopt($curl, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
```
#### Crear formtoken
Se realizará una solicitud POST a la api `https://api.micuentaweb.pe/api-payment/V4/Charge/CreatePayment` con los datos de la compra para generar el formtoken

```php
function formToken(){
    $body = [
        "amount" => $_POST["amount"] * 100,
        "currency" => $_POST["currency"],
        "orderId" => $_POST["orderId"],
        "customer" => [
          "email" => $_POST["email"],
           ...
           ...
          ]
        ],
    ];

    $url = "https://api.micuentaweb.pe/api-payment/V4/Charge/CreatePayment";
    $auth = USERNAME.":".PASSWORD;

    $curl = curl_init($url);
    ...
    ...
    curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
    $raw_response = curl_exec($curl);
    $response = json_decode($raw_response , true);
    return $response;
}

```
#### Visualizar formulario
Se inserta en el header los scripts de la libreria junto al `publicKey`

Header:
```javascript
<script type="text/javascript"
src="https://static.micuentaweb.pe/static/js/krypton-client/V4.0/stable/kr-payment-form.min.js"
kr-public-key="<?= PUBLIC_KEY ?>"
kr-post-url-success="result.php" kr-language="es-Es">
</script>

<link rel="stylesheet" href="https://static.micuentaweb.pe/static/js/krypton-client/V4.0/ext/classic.css">
<script type="text/javascript" src="https://static.micuentaweb.pe/static/js/krypton-client/V4.0/ext/classic.js">
</script>
```
Se inserta en el body la clase `kr-embedded` que deberá tener el parámetro `kr-form-token` generado en la etapa anterior

Body:
```javascript
<div id="micuentawebstd_rest_wrapper">
  <div class="kr-embedded" kr-form-token="<?= $formToken; ?>"></div>
</div>
```


## 2️⃣: Analizar resultado del pago

#### Validación de firma
Se configura una la función `checkhash` que realizará la validación de los datos del parámetro `kr-answer` utilizando una clave de encriptacón definida por el parámetro `kr-hash-key`

```php
function checkHash(){
    if ($_POST['kr-hash-key'] == "sha256_hmac") {
        $key = HMAC_SHA256;
    } elseif ($_POST['kr-hash-key'] == "password") {
        $key = PASSWORD;
    } else {
        return false; 
    }  

    $krAnswer = str_replace('\/', '/',  $_POST["kr-answer"]);
    $calculateHash = hash_hmac("sha256", $krAnswer, $key);

    return ($calculateHash == $_POST["kr-hash"]) ;
}
```

Verificar si la firma recibida es correcta

```php
if (!checkHash()) {
  throw new Exception("Invalid signature");
}
```
En caso afirmativo se puede extraer los datos de kr-answer y mostrar un mensaje indicando que el pago ha sido exitoso

```php
$answer = json_decode($_POST["kr-answer"], true);
```

#### IPN
La IPN es una notificación de servidor a servidor (servidor de Izipay hacia el servidor del comercio) que facilita información en tiempo real y de manera automática cuando se produce un evento, por ejemplo, al registrar una transacción.


Se realiza la verificación de la firma y se devuelve al servidor de izipay un mensaje confirmando el estado del pago.

```php
if (!checkHash()) {
    throw new Exception("Invalid signature");
}

$answer = json_decode($_POST["kr-answer"], true);

$transaction = $answer['transactions'][0];

$orderStatus = $answer['orderStatus'];
$orderId = $answer['orderDetails']['orderId'];
$transactionUuid = $transaction['uuid'];

print 'OK! OrderStatus is ' . $orderStatus;
```

La IPN debe ir configurada en el Backoffice Vendedor, en `Configuración -> Reglas de notificación -> URL de notificación al final del pago`

<p align="center">
  <img src="https://i.postimg.cc/CLqKyHYc/ipn.png" alt="Formulario" width=100%/>
</p>


### Transacción de prueba

Antes de poner en marcha su pasarela de pago en un entorno de producción, es esencial realizar pruebas para garantizar su correcto funcionamiento.

Puede intentar realizar una transacción utilizando una tarjeta de prueba con la barra de herramientas de depuración (en la parte inferior de la página).

<p align="center">
  <img src="https://i.postimg.cc/3xXChGp2/tarjetas-prueba.png" alt="Formulario"/>
</p>

- También puede encontrar tarjetas de prueba en el siguiente enlace. [Tarjetas de prueba](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/kb/test_cards.html)

## 3️⃣:Pase a producción

**Nota**: Reemplace **[CHANGE_ME]** con sus credenciales de PRODUCCIÓN de `API REST` extraídas desde el Back Office Vendedor, ver [Requisitos Previos](#Requisitos_Previos).

- Editar en `keys.example.php` en la ruta raiz del proyecto:
```php
// Identificador de su tienda
define("USERNAME", "~ CHANGE_ME_USER_ID ~");

// Clave de Test o Producción
define("PASSWORD", "~ CHANGE_ME_PASSWORD ~");

// Clave Pública de Test o Producción
define("PUBLIC_KEY","~ CHANGE_ME_PUBLIC_KEY ~");

// Clave HMAC-SHA-256 de Test o Producción
define("HMAC_SHA256","~ CHANGE_ME_HMAC_SHA_256 ~");
```

## 5. Personalización

Si deseas aplicar cambios específicos en la apariencia de la pasarela de pago, puedes lograrlo mediante la modificación de código CSS. En este enlace [Código CSS - Incrustado](https://github.com/izipay-pe/Personalizacion/blob/main/Formulario%20Incrustado/Style-Personalization-Incrustado.css) podrá encontrar nuestro script para un formulario incrustado.

<p align="center">
  <img src="https://i.postimg.cc/zDddmKpH/persona.png" alt="Formulario"/>
</p>

## 6. Consideraciones

Para obtener más información, echa un vistazo a:

- [Formulario incrustado: prueba rápida](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/quick_start_js.html)
- [Primeros pasos: pago simple](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- [Servicios web - referencia de la API REST](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/reference.html)
