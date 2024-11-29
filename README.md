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

## 1. Introducción

En este manual podrás encontrar una guía paso a paso para configurar un proyecto de **[PHP]** con la pasarela de pagos de IZIPAY. Te proporcionaremos instrucciones detalladas y credenciales de prueba para la instalación y configuración del proyecto, permitiéndote trabajar y experimentar de manera segura en tu propio entorno local.
Este manual está diseñado para ayudarte a comprender el flujo de la integración de la pasarela para ayudarte a aprovechar al máximo tu proyecto y facilitar tu experiencia de desarrollo.

> [!IMPORTANT]
> En la última actualización se agregaron los campos: **nombre del tarjetahabiente** y **correo electrónico** (Este último campo se visualizará solo si el dato no se envía en la creación del formtoken).

<p align="center">
  <img src="https://github.com/izipay-pe/Imagenes/blob/main/formulario_incrustado/Imagen-Formulario-Incrustado.png" alt="Formulario" width="350"/>
</p>

#### Este ejemplo es solo una guía para poder realizar la integración de la pasarela de pagos, puede realizar las modificaciones necesarias para su proyecto.

<a name="Requisitos_Previos"></a>

## 2. Requisitos Previos

- Comprender el flujo de comunicación de la pasarela. [Información Aquí](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- Extraer credenciales del Back Office Vendedor. [Guía Aquí](https://github.com/izipay-pe/obtener-credenciales-de-conexion)
- Para este proyecto utilizamos la herramienta Visual Studio Code.
  > [!NOTE]
  > Tener en cuenta que, para que el desarrollo de tu proyecto, eres libre de emplear tus herramientas preferidas.


* Servidor Web
* PHP 7.0 o superior

## 3. Ejecutar ejemplo

### Instalar Xampp u otro servidor local compatible con php

Xampp, servidor web local multiplataforma que contiene los intérpretes para los lenguajes de script de php. Para instalarlo:

1. Dirigirse a la página web de [xampp](https://www.apachefriends.org/es/index.html)
2. Descargarlo e instalarlo.
3. Inicia los servicios de Apache desde el panel de control de XAMPP.

<p align="center">
  <img src="images/panel-control.png" alt="Formulario" />
</p>




### Clonar el proyecto
```sh
git clone https://github.com/izipay-pe/Embedded-PaymentForm-Php.git
``` 

### Datos de conexión 

**Nota**: Reemplace **[CHANGE_ME]** con sus credenciales de `API REST` extraídas desde el Back Office Vendedor, ver [Requisitos Previos](#Requisitos_Previos).

- Editar en `keys.example.php` en la ruta raiz del proyecto:

    ```sh
    // Identificador de su tienda
    IzipayController::setDefaultUsername("~ CHANGE_ME_USER_ID ~");

    // Clave de Test o Producción
    IzipayController::setDefaultPassword("~ CHANGE_ME_PASSWORD ~");

    // Clave Pública de Test o Producción
    IzipayController::setDefaultPublicKey("~ CHANGE_ME_PUBLIC_KEY ~");

    // Clave HMAC-SHA-256 de Test o Producción
    IzipayController::setDefaultHmacSha256("~ CHANGE_ME_HMAC_SHA_256 ~");

    // URL del servidor de Izipay
    IzipayController::setDefaultEndpointApiRest("https://api.micuentaweb.pe");

### Ejecutar proyecto

1. Mover el proyecto y descomprimirlo en la carpeta htdocs en la ruta de instalación de Xampp: `C://xampp/htdocs/[proyecto_php]`

2.  Abrir el navegador web(Chrome, Mozilla, Safari, etc) con el puerto 80 que abrió xampp : `http://localhost:80/[nombre_de_proyecto]` y realizar una compra de prueba.


## 4. Pasos de integración

<p align="center">
  <img src="https://i.postimg.cc/pT6SRjxZ/3-pasos.png" alt="Formulario" />
</p>

## Pasos 1: Desplegar pasarela
#### Etapa 1: Autentificación
Extraer las claves del Backoffice
```php
// Codificar las credenciales en base64
$base64Credentials = base64_encode($credentials);

// Encabezados de la solicitud
$headers = array(
    "Authorization: Basic " . $base64Credentials,
    "Content-Type: application/json"
);
```
#### Etapa 2: Crear formtoken
Se realizará una solicitud POST a nuestra api `https://api.micuentaweb.pe/api-payment/V4/Charge/CreatePayment` con los datos de la compra para generar el formtoken

```php
function formToken(){
    $body = [
        "amount" => $_POST["amount"] * 100,
        "currency" => $_POST["currency"],
        "orderId" => $_POST["orderId"],
        "customer" => [
          "email" => $_POST["email"],
          "billingDetails" => [
            "firstName"=>  $_POST["firstName"],
            "lastName"=>  $_POST["lastName"],
            "phoneNumber"=>  $_POST["phoneNumber"],
            "identityType"=>  $_POST["identityType"],
            "identityCode"=>  $_POST["identityCode"],
            "address"=>  $_POST["address"],
            "country"=>  $_POST["country"],
            "city"=>  $_POST["city"],
            "state"=>  $_POST["state"],
            "zipCode"=>  $_POST["zipCode"],
          ]
        ],
    ];

    $url = "https://api.micuentaweb.pe/api-payment/V4/Charge/CreatePayment";
    $auth = USERNAME.":".PASSWORD;

    $curl = curl_init($url);
    curl_setopt($curl, CURLOPT_HEADER, false);
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($curl, CURLOPT_HTTPHEADER, array("Content-type: application/json"));
    curl_setopt($curl, CURLOPT_POST, true);
    curl_setopt($curl, CURLOPT_USERPWD, $auth);
    curl_setopt($curl, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
    curl_setopt($curl, CURLOPT_POSTFIELDS, json_encode($body));
    curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, false);
    curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
    $raw_response = curl_exec($curl);
    $response = json_decode($raw_response , true);
    return $response;
}

```


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

#### Etapa 2: Crear formtoken
Se realizará una solicitud POST a nuestra api

## Pasos 2: Analizar resultado del pago
#### Etapa 6: IPN
La IPN es una notificación de servidor a servidor (servidor de Izipay hacia el servidor del comercio) que facilita información en tiempo real y de manera automática cuando se produce un evento, por ejemplo, al registrar una transacción.
Los datos transmitidos en la IPN se reciben y analizan mediante un script que el vendedor habrá desarrollado en su servidor.


```php
<?php
require_once "keys.example.php";

if (empty($_POST)) {
    throw new Exception("No post data received!");
}

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

La IPN debe ir configurada en el Backoffice Vendedor, en Configuración -> Reglas de notificación -> URL de notificación al final del pago

<p align="center">
  <img src="https://i.postimg.cc/CLqKyHYc/ipn.png" alt="Formulario" width=100%/>
</p>


### Pruebas en test
Transacción de prueba

Antes de poner en marcha su pasarela de pago en un entorno de producción, es esencial realizar pruebas para garantizar su correcto funcionamiento.

Puede intentar realizar una transacción utilizando una tarjeta de prueba con la barra de herramientas de depuración (en la parte inferior de la página).

<p align="center">
  <img src="https://i.postimg.cc/3xXChGp2/tarjetas-prueba.png" alt="Formulario"/>
</p>

- También puede encontrar tarjetas de prueba en el siguiente enlace. [Tarjetas de prueba](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/kb/test_cards.html)

## Pasos 3: Pase a producción

**Nota**: Reemplace **[CHANGE_ME]** con sus credenciales de `API REST` extraídas desde el Back Office Vendedor, ver [Requisitos Previos](#Requisitos_Previos).

- Editar en `keys.example.php` en la ruta raiz del proyecto:

    ```sh
    // Identificador de su tienda
    IzipayController::setDefaultUsername("~ CHANGE_ME_USER_ID ~");

    // Clave de Test o Producción
    IzipayController::setDefaultPassword("~ CHANGE_ME_PASSWORD ~");

    // Clave Pública de Test o Producción
    IzipayController::setDefaultPublicKey("~ CHANGE_ME_PUBLIC_KEY ~");

    // Clave HMAC-SHA-256 de Test o Producción
    IzipayController::setDefaultHmacSha256("~ CHANGE_ME_HMAC_SHA_256 ~");

    // URL del servidor de Izipay
    IzipayController::setDefaultEndpointApiRest("https://api.micuentaweb.pe");


## 5. Personalización

Si deseas aplicar cambios específicos en la apariencia de la pasarela de pago, puedes lograrlo mediante la modificación de código CSS. En este enlace [Código CSS - Incrustado](https://github.com/izipay-pe/Personalizacion/blob/main/Formulario%20Incrustado/Style-Personalization-Incrustado.css) podrá encontrar nuestro script para un formulario incrustado.

## 6. Consideraciones

Para obtener más información, echa un vistazo a:

- [Formulario incrustado: prueba rápida](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/quick_start_js.html)
- [Primeros pasos: pago simple](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- [Servicios web - referencia de la API REST](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/reference.html)
