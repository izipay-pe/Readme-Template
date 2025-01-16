<p align="center">
  <img src="https://github.com/izipay-pe/Imagenes/blob/main/logos_izipay/logo-izipay-banner-1140x100.png?raw=true" alt="Formulario" width=100%/>
</p>

# Redirect-PaymentForm-JavaScript
## Proyectos por Lenguaje

## Índice

➡️ [1. Introducción](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#%EF%B8%8F-1-introducci%C3%B3n)  
🔑 [2. Requisitos previos](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#-2-requisitos-previos)  
🚀 [3. Ejecutar ejemplo](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#-3-ejecutar-ejemplo)  
🔗 [4. Pasos de integración](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#4-pasos-de-integraci%C3%B3n)  
💻 [4.1. Desplegar pasarela](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#41-desplegar-pasarela)  
💳 [4.2. Analizar resultado de pago](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#42-analizar-resultado-del-pago)  
📡 [4.3. Pase a producción](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#43pase-a-producci%C3%B3n)  
🎨 [5. Personalización](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#-5-personalizaci%C3%B3n)  
🛠️ [6. Servidores](https://github.com/izipay-pe/Readme-Template/blob/main/README.md#-6-servidores)    
📚 [7. Consideraciones](https://github.com/izipay-pe/Readme-Template/tree/main?tab=readme-ov-file#-6-consideraciones)

## ➡️ 1. Introducción

En este manual encontrarás una guía detallada para configurar un proyecto en **[JavaScript puro]** integrado con la pasarela de pagos de IZIPAY. Te proporcionaremos instrucciones claras y credenciales de prueba para instalar y configurar el proyecto, permitiéndote trabajar y realizar pruebas de manera segura en tu propio entorno local.
Este manual está diseñado para facilitar la comprensión del flujo de integración de la pasarela de pagos y maximizar el rendimiento de tu desarrollo front-end. Ten en cuenta que este proyecto se conecta a un servidor (backend) para gestionar las operaciones críticas relacionadas con la pasarela de pagos.

<p align="center">
  <img src="https://github.com/izipay-pe/Imagenes/blob/main/formulario_redireccion/Imagen-Formulario-Redireccion.png?raw=true" alt="Formulario" width="750"/>
</p>

## 🔑 2. Requisitos Previos

- Comprender el flujo de comunicación de la pasarela. [Información Aquí](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- Extraer credenciales del Back Office Vendedor. [Guía Aquí](https://github.com/izipay-pe/obtener-credenciales-de-conexion)
- Descargar y ejecutar un servidor (Back) de redirección.[Servidores disponibles](https://github.com/izipay-pe/Readme-Template/blob/main/README.md#-6-servidores)
- Para este proyecto utilizamos la herramienta Visual Studio Code.
> [!NOTE]
> Tener en cuenta que, para que el desarrollo de tu proyecto, eres libre de emplear tus herramientas preferidas.

## 🚀 3. Ejecutar ejemplo


### Clonar el proyecto
```sh
git clone https://github.com/izipay-pe/Redirect-PaymentForm-JavaScript.git
``` 

### Datos de conexión 

Realice la conexión al servidor modificando la ruta `http://localhost:3000/redirect` mostrada a continuación.

- Editar el archivo `js/capturaFormulario.js` en la ruta raiz del proyecto:
```js
const response = await fetch ('http://localhost:3000/redirect',{
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(data),
      });

```

### Ejecutar proyecto

1. Ejecuta el siguiente comando para instalar todas las dependencias necesarias:
```bash
npm install
```

2.  Iniciar la aplicación:
```bash
http-server
```

## 🔗4. Pasos de integración

<p align="center">
  <img src="https://i.postimg.cc/pT6SRjxZ/3-pasos.png" alt="Formulario" />
</p>

## 💻4.1. Desplegar pasarela
### Autentificación
Las claves de acceso del Backoffice Vendedor deben configurarse exclusivamente en el servidor (backend), no en la aplicación **[JavaScript]**. Esto asegura que las credenciales sensibles permanezcan protegidas y no sean expuestas en el código. 

ℹ️ Para más información: [Autentificación](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/embedded/keys.html)
### Crear formtoken
Para configurar la pasarela, es necesario generar un formtoken. Esto se realiza mediante una solicitud API desde tu aplicación **[JavaScript]** al servidor (backend), el cual procesa los datos de la compra y devuelve el formtoken necesario. Estos son los datos de compra necesarios para generar el formtoken.
```node
  <form id="formulario" class="col-md-12">
                <div class="row">
                    <!-- Datos del cliente -->
                    <div class="left-column col-md-6">
                        <section class="customer-details">
                            <h2>Datos del cliente</h2>
                            <!-- Nombre -->
                            <div class="form-row">
                                <div class="form-group col-md-6">
                                    <label for="firstName">Nombre</label>
                                    <input type="text" class="form-control" id="firstName" name="firstName" placeholder="Nombre" required />
                                </div>
                                <!-- Apellido -->
                                <div class="form-group col-md-6">
                                    <label for="lastName">Apellido</label>
                                    <input type="text" class="form-control" id="lastName" name="lastName" placeholder="Apellido" required />
                                </div>
                            </div>
                            <!-- Correo electronico -->
                            <div class="form-group">
                                <label for="email">Correo Electrónico</label>
                                <input type="email" class="form-control" id="email" name="email" placeholder="Email" required />
                            </div>
                            <!-- Telefono -->
                            <div class="form-group">
                                <label for="phoneNumber">Teléfono</label>
                                <input type="tel" class="form-control" id="phoneNumber" name="phoneNumber" placeholder="999999999" required />
                            </div>
                            <!-- Tipo de Documento -->
                            <div class="form-row">
                                <div class="form-group col-md-4">
                                    <label for="identityType">Tipo de Documento</label>
                                    <select class="form-control" id="identityType" name="identityType">
                                        <option value="DNI">DNI</option>
                                        <option value="PS">Pasaporte</option>
                                        <option value="CE">Carné de Extranjería</option>
                                    </select>
                                </div>
                                <!-- Documento -->
                                <div class="form-group col-md-8">
                                    <label for="identityCode">Documento</label>
                                    <input type="text" class="form-control" id="identityCode" name="identityCode" placeholder="Doc. Identidad" required />
                                </div>
                            </div>
                        </section>

                        <!-- Datos de envío -->
                        <section class="billing-details">
                            <h2>Datos de envío</h2>
                            <!-- Direccion -->
                            <div class="form-group">
                                <label for="address">Dirección</label>
                                <input type="text" class="form-control" id="address" name="address" placeholder="Nombre de la calle y número de casa" required />
                            </div>
                            <!-- Pais -->
                            <div class="form-row">
                                <div class="form-group col-md-6">
                                    <label for="country">País</label>
                                    <select class="form-control" id="country" name="country">
                                        <option value="PE">Perú</option>
                                        <option value="AR">Argentina</option>
                                        <option value="CL">Chile</option>
                                        <option value="CO">Colombia</option>
                                    </select>
                                </div>
                                <!-- Departamento -->
                                <div class="form-group col-md-6">
                                    <label for="state">Departamento</label>
                                    <input type="text" class="form-control" id="state" name="state" placeholder="Departamento" required />
                                </div>
                            </div>
                            <div class="form-row">
                                <!-- Distrito -->
                                <div class="form-group col-md-6">
                                    <label for="city">Distrito</label>
                                    <input type="text" class="form-control" id="city" name="city" placeholder="Distrito" required />
                                </div>
                                <!-- Codigo Postal -->
                                <div class="form-group col-md-6">
                                    <label for="zipCode">Código Postal</label>
                                    <input type="text" class="form-control" id="zipCode" name="zipCode" placeholder="15021" required />
                                </div>
                            </div>
                        </section>
                    </div>

                    <!-- Datos del pago -->
                    <div class="right-column col-md-6">
                        <section class="customer-details">
                            <h2>Datos del pago</h2>
                            <!-- OrderId -->
                            <div class="form-group">
                                <label for="orderId">Order-id</label>
                                <input type="text" class="form-control" id="orderId" name="orderId" required />
                            </div>
                            <!-- Monto -->
                            <div class="form-group">
                                <label for="amount">Monto</label>
                                <input type="number" class="form-control" id="amount" name="amount" placeholder="0.00" step="0.01" min="0" required />
                            </div>
                            <!-- Moneda -->
                            <div class="form-group">
                                <label for="currency">Moneda</label>
                                <select class="form-control" id="currency" name="currency">
                                    <option value="604" data-display="Soles">Soles</option>
                                    <option value="840" data-display="Dólares">Dólares</option>
                                </select>
                            </div> 

```
Estos datos son capturados en `js/capturaFormulario.js`:

```javascript

document.getElementById('formulario').addEventListener('submit', async function (event) {
event.preventDefault();

```


### Visualizar formulario
Para desplegar la pasarela mediante redirección, es necesario recepcionar todos los datos que nos devuelve el servidor para enviárselos a IZIPAY mediante un formulario detallado a continuación en el archivo `checkout.html`

```javascript
<!-- Formulario con los datos de pago -->
				<form class="from-checkout" action="https://secure.micuentaweb.pe/vads-payment/" method="post">
				<!-- Inputs generados dinámicamente -->
				<input type="hidden" name="vads_action_mode" id="vads_action_mode" />
				<input type="hidden" name="vads_amount"  id="vads_amount" />
				<input type="hidden" name="vads_ctx_mode"  id="vads_ctx_mode" />
				<input type="hidden" name="vads_currency"  id="vads_currency" />
				<input type="hidden" name="vads_cust_address"  id="vads_cust_address" />
				<input type="hidden" name="vads_cust_cell_phone"  id="vads_cust_cell_phone" />
				<input type="hidden" name="vads_cust_city"  id="vads_cust_city" />
				<input type="hidden" name="vads_cust_country"  id="vads_cust_country" />
				<input type="hidden" name="vads_cust_email"  id="vads_cust_email" />
				<input type="hidden" name="vads_cust_first_name"  id="vads_cust_first_name" />
				<input type="hidden" name="vads_cust_last_name"  id="vads_cust_last_name" />
				<input type="hidden" name="vads_cust_national_id"  id="vads_cust_national_id" />
				<input type="hidden" name="vads_cust_state"  id="vads_cust_state" />
				<input type="hidden" name="vads_cust_zip"  id="vads_cust_zip" />
				<input type="hidden" name="vads_order_id"  id="vads_order_id" />
				<input type="hidden" name="vads_page_action"  id="vads_page_action" />
				<input type="hidden" name="vads_payment_config"  id="vads_payment_config" />
				<input type="hidden" name="vads_redirect_success_timeout"  id="vads_redirect_success_timeout" />
				<input type="hidden" name="vads_return_mode"  id="vads_return_mode" />
				<input type="hidden" name="vads_site_id"  id="vads_site_id" />
				<input type="hidden" name="vads_trans_date"  id="vads_trans_date" />
				<input type="hidden" name="vads_trans_id"  id="vads_trans_id" />
				<input type="hidden" name="vads_url_success"  id="vads_url_success" />
				<input type="hidden" name="vads_version"  id="vads_version" />
				<input type="hidden" name="signature"  id="signature" />
				<button class="btn btn-checkout" type="submit" name="pagar">Pagar</button>
				</form>	

```

Estos datos son recepcionados a través de `js/recepcionFormulario.js`: 

```javascript

   //Llenamos los datos a envíar
          for (const key in serverData.parameters) {
            const elementId = key;
            const element = document.getElementById(elementId);
            element.value = serverData.parameters[key];
          }
        } catch (error) {
          console.error('Error al analizar los datos de sessionStorage:', error);
        }
```

## 💳4.2. Analizar resultado del pago

### Validación de firma
Para la validación de firma, es necesario llamar al servidor nuevamente y cambiar la dirección en el archivo `js/resultadoCompra.js`. Esto valida los datos recibidos por el servidor y te devolverá un booleano "true" si los datos son correctos, o "false" si los datos son incorrectos.
```node
 // Enviar una petición POST al servidor para validar la Firma
  const respuesta = await fetch('http://localhost:3000/checkSignature', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json; charset=UTF-8',
      'Accept': 'application/json'
  },
    body: jsonString,
  });
```

Se valida que la firma recibida es correcta

```node
  // Verificar si la respuesta del servidor es exitosa
  if (!respuesta.ok) {
    throw new Error(`Error en la solicitud: ${respuesta.statusText}`);
  }
```
En caso que la validación sea exitosa, se puede extraer los datos de a través de un JSON y mostrar los datos del pago realizado.
```node
 const datosRespuesta = await respuesta.json();
  //Si es exitosa, imprimimos la firma
  if(datosRespuesta == true) { 
    const preElement = document.getElementById('pre_kash');
    preElement.textContent = jsonString; 
  }

} catch (error) {
  console.error('Error al validar la firma:', error);
  console.error('JSON enviado:', JSON.stringify(datosAEnviar, null, 2)); // Imprimir JSON en caso de error
}
```

ℹ️ Para más información: [Analizar resultado del pago](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/kb/payment_done.html)

### Transacción de prueba

Antes de poner en marcha su pasarela de pago en un entorno de producción, es esencial realizar pruebas para garantizar su correcto funcionamiento. 

Puede intentar realizar una transacción utilizando una tarjeta de prueba (en la parte inferior del formulario).

<p align="center">
  <img src="https://github.com/izipay-pe/Imagenes/blob/main/formulario_redireccion/Imagen-Formulario-Redireccion-testcard.png?raw=true" alt="Tarjetas de prueba" width="450"/>
</p>

- También puede encontrar tarjetas de prueba en el siguiente enlace. [Tarjetas de prueba](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/kb/test_cards.html)

## 📡4.3.Pase a producción

Para el pase a producción es necesario cambiar las credenciales de TEST por las de PRODUCCIÓN dentro del servidor utilizado. El identificador de tienda sigue siendo el mismo.

## 🎨 5. Personalización

Si deseas aplicar cambios específicos en la apariencia de la página de pago, puedes lograrlo mediante las opciones de personalización en el Backoffice. En este enlace [Personalización - Página de pago](https://youtu.be/hy877zTjpS0?si=TgSeoqw7qiaQDV25) podrá encontrar un video para guiarlo en la personalización.

<p align="center">
  <img src="https://github.com/izipay-pe/Imagenes/blob/main/formulario_redireccion/Personalizacion-formulario-redireccion.png?raw=true" alt="Personalizacion de formulario en redireccion"  width="750" />
</p>


## 🛠 6. Servidores
Lista de servidores disponibles:

| Lenguaje | Proyecto                                                                 |
|---------------------|--------------------------------------------------------------------------|
| ![PHP](https://img.shields.io/badge/PHP-777BB4?style=flat&logo=php&logoColor=white)          | [Server-Redirect-PHP](enlace_a_proyecto_php)                                           |
| ![Laravel](https://img.shields.io/badge/Laravel-FF2D20?style=flat&logo=laravel&logoColor=white) | [Server-Redirect-Laravel](enlace_a_proyecto_laravel)                                   |
| ![Django](https://img.shields.io/badge/Django-092E20?style=flat&logo=django&logoColor=white)  | [Server-Redirect-Django](enlace_a_proyecto_django)                                     |
| ![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)    | [Server-Redirect-Flask](enlace_a_proyecto_flask)                                       |
| ![.NET](https://img.shields.io/badge/.NET-5C2D91?style=flat&logo=dotnet&logoColor=white)      | [Server-Redirect-.NET](enlace_a_proyecto_dotnet)                                       |
| ![NodeJS](https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white) | [Server-Redirect-NodeJS](enlace_a_proyecto_nodejs)                                     |
| ![NextJS](https://img.shields.io/badge/Next.js-000000?style=flat&logo=nextdotjs&logoColor=white) | [Server-Redirect-NextJS](enlace_a_proyecto_nextjs)                                     |
| ![Java](https://img.shields.io/badge/Servlet%20Java-007396?style=flat&logo=java&logoColor=white) | [Server-Redirect-Servelt-Java](enlace_a_proyecto_servlet_java)                         |
| ![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=flat&logo=springboot&logoColor=white) | [Server-Redirect-Springboot-Java](enlace_a_proyecto_spring_boot)                         |


## 📚 7. Consideraciones

Para obtener más información, echa un vistazo a:

- [Integración Formulario redirección](https://secure.micuentaweb.pe/doc/es-PE/form-payment/standard-payment/sitemap.html)
- [Primeros pasos: pago simple](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- [Servicios web - referencia de la API REST](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/reference.html)
