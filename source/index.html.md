---
title: Social & Loyal

language_tabs:
  - shell
  - php
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

#includes:
  #- errors

search: true
---

# API S&L
##Bienvenido

Bienvenido a la API de Social&Loyal. Aquí encontrarás toda la documentación necesaria para hacer un uso correcto de ella.

Las **claves de la API** se encuentran en el Admin de la aplicación, en el apartado 'Configuración de la app' > 'Developers' > 'Claves API'


API Endpoint

Todas las URLs listadas en esta documentación son referidas a : **https://api2.socialandloyal.com/**.
Por ejemplo, la llamada /users está accesible en **https://api2.socialandloyal.com/users**

RESTful

La API S&L es en su mayoría [RESTful](https://en.wikipedia.org/wiki/Representational_state_transfer#RESTful_web_services). Cualquier respuesta distinta de HTTP 200 se puede considerar un error. Los datos devueltos contendrán información sobre el error.

## Autenticación

La autenticación se hace mediante el esquema de clave pública/clave privada y "HMAC hashing".

* Usando la clave privada, se crea un hash basado en el contenido de la petición.
* En los headers se envía este hash, la clave pública y el timestamp de la fecha-hora.
* El servidor recoge la clave pública y localiza la clave privada.
* El servidor usa las claves pública y privada y el timestamp para generar el hash que se comparará con el enviado y validar la petición si coinciden.

# Código
##PHP

```python

GET request

	$public_key = 'YOUR-PUBLIC-KEY';
	$private_key = 'YOUR-PRIVATE-KEY';
	$params_data = array(
        'client_name' => 'YOUR-CLIENT-NAME',
        'date_ini' => '2014-01-01 10:00:00',
        'date_end' => '2014-02-04 23:59:59'
        );
	$timestamp = microtime(true);
	$hash = hash_hmac('sha1', $public_key.$timestamp, $private_key);
	$headers = array(
        'X-MICROTIME: '.$timestamp,
        'X-PUBLIC: '.$public_key,
        'X-HASH: '.$hash
	);

	// *************
	// GET /users
	// *************
	$params = http_build_query($params_data);
	$url = 'https://api2.socialandloyal.com/users?'.$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";

POST request

	$public_key = 'YOUR-PUBLIC-KEY';
	$private_key = 'YOUR-PRIVATE-KEY';
	$timestamp = microtime(true);
	$hash = hash_hmac('sha1', $public_key.$timestamp, $private_key);
	$headers = array(
        'X-MICROTIME: '.$timestamp,
        'X-PUBLIC: '.$public_key,
        'X-HASH: '.$hash
	);
	// *************************
	// POST /promocodes
	// *************************
	$params_data = array(
            'client_name' => 'YOUR-CLIENT-NAME',
            'date_ini' => '2014-06-01 10:00:00',
            'date_end' => '2014-06-30 23:59:59',
            'name' => 'lote test',
            'points' => 25,
            'qty' => 4,
            'uses' => 1,
    );
	$post_data ='';
	foreach ($params_data as $k => $v) {
    $post_data .= $k . '='.$v.'&';
	}
	rtrim($post_data, '&');
	$url = 'https://api2.socialandloyal.com/promocodes';
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($ch, CURLOPT_POST, count($post_data));
	curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
	$result = curl_exec($ch);
	curl_close($ch);

	echo "RESULT\n======\n". $result."\n\n";
	var_dump(json_decode($result, true));

```

## Javascript

```javascript

  <script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/	jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
        // GET users
        var timestamp = $.now();
        var params_data = {
            'client_name': 'YOUR_CLIENT_NAME'
        };

        var getHMAC = function(key, timestamp) {
            var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
            return hash.toString();
        };

        // GET users
        $.ajax({
            type: 'GET',
            url: 'https://api2.socialandloyal.com/users',
            data: params_data,
            beforeSend: function (request) {
                request.setRequestHeader('X-MICROTIME', timestamp);
                request.setRequestHeader('X-PUBLIC', 'YOUR_API_PUBLIC');
                request.setRequestHeader('X-HASH', getHMAC('YOUR_API_PUBLIC', timestamp));
            },
            success: function(response){
                if(!response) {
                    // error
                } else {
                    // manage response
                }
            },
            error: function(response) {
                // manage error response from API
                // JSON.stringify(response.responseText)
            },
            dataType: 'json'
        });
	</script>

```

# Ejemplos
## Completar una compra

**Paso 1**

En primer lugar, debemos comprobar si el usuario existe en nuestra plataforma.

Para ello, podemos usar el método **GET /users/{external_id}**, donde {external_id} es el id de usuario en la plataforma cliente.

[http://developers.socialandloyal.com/index.php#users_get_externalid](http://developers.socialandloyal.com/index.php#users_get_externalid)

Si el usuario no existe en nuestra plataforma, debemos crearlo, con el parámetro **external_id**.

Para ello, podemos usar el método **POST /users**

[http://developers.socialandloyal.com/index.php#users_post](http://developers.socialandloyal.com/index.php#users_post)

**Paso 2**

Registrar la compra.

Para ello debemos utilizar la llamada:

[http://developers.socialandloyal.com/index.php#users_post_transaction](http://developers.socialandloyal.com/index.php#users_post_transaction)

## Recoger los puntos de un usuario

**Paso 1**

En primer lugar, debemos comprobar si el usuario existe en nuestra plataforma.

Para ello, podemos usar el método **GET /users/{external_id}**, donde {external_id} es el id de usuario en la plataforma cliente.

[http://developers.socialandloyal.com/index.php#users_get_externalid](http://developers.socialandloyal.com/index.php#users_get_externalid)

Si el usuario no existe en nuestra plataforma, debemos crearlo, con el parámetro **external_id**.

Para ello, podemos usar el método **POST /users**

[http://developers.socialandloyal.com/index.php#users_post](http://developers.socialandloyal.com/index.php#users_post)

**Paso 2**

En segundo lugar, debemos realizar la llamada al método

[http://developers.socialandloyal.com/index.php#users_get_externalid](http://developers.socialandloyal.com/index.php#users_get_externalid)

que nos devolverá un json con los datos del usuario (points).

## Integrar iframe
### Introduccion

Este recurso permite "incrustar" en su página web de una sencilla manera un iframe a nuestra plataforma.

Se deben pasar los parámetros **external_id** y **token**, como parámetros GET, a la URL del iframe. Opcionalmente se puede pasar el parámetro **locale** (ejemplo: es_es) para indicar el idioma del usuario.

De esta manera, los usuarios pueden acceder a la aplicación sin realizar el proceso de registro.

### How to do

**Paso 1**

En primer lugar, debemos comprobar si el usuario existe en nuestra plataforma.

Para ello, podemos usar el método **GET /users/{external_id}**, donde {external_id} es el id de usuario en la plataforma cliente.

[http://developers.socialandloyal.com/index.php#users_get_externalid](http://developers.socialandloyal.com/index.php#users_get_externalid)

Si el usuario no existe en nuestra plataforma, debemos crearlo, con el parámetro **external_id**.

Para ello, podemos usar el método **POST /users**

[http://developers.socialandloyal.com/index.php#users_post](http://developers.socialandloyal.com/index.php#users_post)

**Paso 2**

Obtener el widget token.

Para obtenerlo, debemos utilizar el método **GET /widgetToken**.

[http://developers.socialandloyal.com/index.php#widgettoken](http://developers.socialandloyal.com/index.php#widgettoken)

**Paso 3**

Incrustar el iframe en la página web.

**Parámetros:**

* **external_id**: External id del usuario
* **token**: Widget token

_Ejemplo_

```<iframe id="loginIframe" src="https://[CLIENT_NAME].socialandloyal.com/?external_id=[external_id]&token=[token]&locale=es_es" width="100%" scrolling="no" frameborder="0"></iframe>
```

**Incluir código iframe resizer**

```javascript
_Codigo javascript - con jQuery_

     <script src="https://[CLIENT_NAME].socialandloyal.com/js/front/vendors/iframe/iframeResizer.min.js"></script>
     <script type="text/javascript">$('iframe#loginIframe').iFrameResize([{sizeHeight: true}]);</script>
```

```javascript
_Codigo javascript - sin jQuery_

     <script src="https://[CLIENT_NAME].socialandloyal.com/js/front/vendors/iframe/iframeResizer.min.js"></script>
     <script type="text/javascript">iFrameResize([{sizeHeight: true}],['iframe#loginIframe']);</script>
```

# Datos de usuarios
## GET /users/

Listado de usuarios del cliente. Si se envía como parámetros fecha/hora de inicio y fin, devuelve los que tienen movimiento en el intervalo pedido.

**Por defecto no se devuelven datos de superusuarios**.

Para obtenerlos hay que enviar el parámetro admin_data = 1.

* Parámetro/s requerido/s: client_name
* Parámetro/s opcional/es: date_ini, date_end, admin_data

_Ejemplos de código:_

### PHP

```python

	$public_key = 'YOUR-PUBLIC-KEY';
	$private_key = 'YOUR-PRIVATE-KEY';
	$params_data = array(
        'client_name' => 'YOUR-CLIENT-NAME',
        'date_ini' => '2014-01-01 10:00:00',
        'date_end' => '2014-02-04 23:59:59'
        );
	$timestamp = microtime(true);
	$hash = hash_hmac('sha1', $public_key.$timestamp, $private_key);
	$headers = array(
        'X-MICROTIME: '.$timestamp,
        'X-PUBLIC: '.$public_key,
        'X-HASH: '.$hash
	);

	// *************
	// GET /users
	// *************
	$params = http_build_query($params_data);
	$url = 'https://api2.socialandloyal.com/users?'.$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
```

###Javascript

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
        // GET users
        var timestamp = $.now();
        var params_data = {
            'client_name': 'YOUR_CLIENT_NAME'
        };

        var getHMAC = function(key, timestamp) {
            var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
            return hash.toString();
        };

        // GET users
        $.ajax({
            type: 'GET',
            url: 'https://api2.socialandloyal.com/users',
            data: params_data,
            beforeSend: function (request) {
                request.setRequestHeader('X-MICROTIME', timestamp);
                request.setRequestHeader('X-PUBLIC', 'YOUR_API_PUBLIC');
                request.setRequestHeader('X-HASH', getHMAC('YOUR_API_PUBLIC', timestamp));
            },
            success: function(response){
                if(!response) {
                    // error
                } else {
                    // manage response
                }
            },
            error: function(response) {
                // manage error response from API
                // JSON.stringify(response.responseText)
            },
            dataType: 'json'
        });
	</script>
```

```javascript
Respuesta (con resultados):

	{
	"data":[
    	{
    	"id":"17189",
    	"name":"Test",
    	"surname":"SocialLoyal",
    	"email":"test@socialandloyal.com",
    	"birthday":"1971-02-15",
    	"gender":"female",
    	"location":"",
    	"points":"195"},
    	{
    	"id":"17194",
    	"name":"Test2",
    	"surname":"SocialAndLoyal",
    	"surname":"Toomble",
    	"email":"test@gmail.com",
    	"birthday":"1980-04-14",
    	"gender":"male",
    	"location":"Barcelona,
    	Spain",
    	"points":"150"}
    	]
	}
```
```javascript
Respuesta (vacía):

	{"data":[] }
```
## GET /users/{user_id}

Datos de un usuario (búsqueda por id de usuario).

* Parámetro/s requerido/s: client_name

Ejemplos de código:

```python
	$user_id = 1234;
	$public_key = 'YOUR-PUBLIC-KEY';
	$private_key = 'YOUR-PRIVATE-KEY';
	$params_data = array(
        'client_name' => 'YOUR-CLIENT-NAME',
        );
	$timestamp = microtime(true);
	$hash = hash_hmac('sha1', $public_key.$timestamp, $private_key);
	$headers = array(
        'X-MICROTIME: '.$timestamp,
        'X-PUBLIC: '.$public_key,
        'X-HASH: '.$hash
	);

	// *************
	// GET /users
	// *************
	$params = http_build_query($params_data);
	$url = "https://api2.socialandloyal.com/users/$user_id?".$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
```
```javascript

	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
    // GET users/user_id
        var user_id = 1234;
        var timestamp = $.now();
        var params_data = {
            'client_name': 'YOUR_CLIENT_NAME'
        };

        var getHMAC = function(key, timestamp) {
            var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
            return hash.toString();
        };

        // GET users
        $.ajax({
            type: 'GET',
            url: 'https://api2.socialandloyal.com/users' + user_id,
            data: params_data,
            beforeSend: function (request) {
                request.setRequestHeader('X-MICROTIME', timestamp);
                request.setRequestHeader('X-PUBLIC', 'YOUR_API_PUBLIC');
                request.setRequestHeader('X-HASH', getHMAC('YOUR_API_PUBLIC', timestamp));
            },
            success: function(response){
                if(!response) {
                    // error
                } else {
                    // manage response
                }
            },
            error: function(response) {
                // manage error response from API
                // JSON.stringify(response.responseText)
            },
            dataType: 'json'
        });
	</script>
```
```javascript
Respuesta (con resultados):

	{
    	"data":{
        	"id":"1234",
        	"name":"Test",
        	"surname":"SocialAndLoyal",
        	"email":"test@socialandloyal.com",
        	"facebook_id":"100007083517802",
        	"birthday":"1989-02-15",
        	"gender":"female",
        	"location":"Bcn",
        	"points":"7582"
    	}
	}
```
```javascript
Respuesta (vacía):

	{"data":[] }
```
