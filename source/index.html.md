---
title: Social & Loyal

language_tabs:
  - shell
  - php: PHP
  - javascript: Javascript

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

> GET request

```php
<?php
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
?>
```

> POST request

```php
<?php

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
?>
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

> Ejemplo

```json
<iframe id="loginIframe" src="https://[CLIENT_NAME].socialandloyal.com/?external_id=[external_id]&token=[token]&locale=es_es" width="100%" scrolling="no" frameborder="0"></iframe>
```

> Incluir código iframe resizer

```javascript
_Codigo javascript - con jQuery_

  <script src="https://[CLIENT_NAME].socialandloyal.com/js/front/vendors/iframe/iframeResizer.min.js"></script>
  <script type="text/javascript">$('iframe#loginIframe').iFrameResize([{sizeHeight: true}]);</script>

_Codigo javascript - sin jQuery_

  <script src="https://[CLIENT_NAME].socialandloyal.com/js/front/vendors/iframe/iframeResizer.min.js"></script>
  <script type="text/javascript">iFrameResize([{sizeHeight: true}],['iframe#loginIframe']);</script>
```

Incrustar el iframe en la página web.

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
external_id| | | External id del usuario
token | | | Widget token

# Datos de usuarios

## GET /users/

```php
<?php
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
?>
```

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

> Respuesta (con resultados):

```javascript
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

> Respuesta (vacía):

```javascript
	{
    "data":[]
  }
```

Listado de usuarios del cliente. Si se envía como parámetros fecha/hora de inicio y fin, devuelve los que tienen movimiento en el intervalo pedido.

**Por defecto no se devuelven datos de superusuarios**.

Para obtenerlos hay que enviar el parámetro admin_data = 1.

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
date_ini | | false |
date_end | | false |
admin_data | | false |

## GET /users/{user_id}

```php
<?php
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
?>
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

> Respuesta (con resultados):

```javascript
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

> Respuesta (vacía):

```javascript
	{"data":[] }
```

Datos de un usuario (búsqueda por id de usuario).

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |

## GET /users/email/{email}

```php
<?php
	$email = 'test@socialandloyal.com';
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
	$url = "https://api2.socialandloyal.com/users/email/$email?".$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
?>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
    // GET users/user_id
        var email = 'test@socialandloyal.com';
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
            url: 'https://api2.socialandloyal.com/users/email/' + email,
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

> Respuesta (con resultados):

```javascript
	{
		"data":{
    		"id":"32256",
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

> Respuesta (vacía):

```javascript
	{"data":[] }
```

Datos de un usuario (búsqueda por email).

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |


## GET /users/external_id/{external_id}

```php
<?php  
	$external_id = 1111;
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
	$url = "https://api2.socialandloyal.com/users/external_id/$external_id?".$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
?>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
    // GET users/user_id
        var external_id = 1111;
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
            url: 'https://api2.socialandloyal.com/users/external_id' + external_id,
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

> Respuesta (con resultados):

```javascript
	{
    	"data":{
        	"id":"32256",
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

> Respuesta (vacía):

```javascript

	{"data":[] }
```

Datos de un usuario (búsqueda por external_id).

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |


## GET /users/{user_id}/movements

```php
<?php  
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
	$url = "https://api2.socialandloyal.com/users/$user_id/movements?".$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
?>
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
            url: 'https://api2.socialandloyal.com/users/' + user_id + '/movements',
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
> Respuesta:

```javascript
	{
    	"data":[
        	{"type":"Promocode",
        	"desc":"Código usado: 1234",
        	"points":"25",
        	"added":"2014-01-29 13:32:42"
        	},
        	{"type":"Pointget",
        	"desc":"Visita nuestra web y consigue puntos",
        	"points":"20",
        	"added":"2014-01-29 13:32:25"
        	},
        	{"type":"FbRequest",
        	"desc":"Amigo invitado",
        	"points":"7",
        	"added":"2014-01-22 11:20:34"
        	},
        	{"type":"Pointget",
        	"desc":"Siguenos en facebook",
        	"points":"13",
        	"added":"2014-01-21 17:01:34"
        	}
        ]
	}
```

Lista de movimientos de un usuario. Si se envía como parámetros fecha/hora de inicio y fin, devuelve los movimientos en el intervalo pedido.

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
date_ini | | false |
date_end | | false |

## GET /users/external_id/{external_id}/movements

```php
<?php  
	$external_id = 1111;
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
	$url = "https://api2.socialandloyal.com/users/external_id/$external_id/movements?".$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
?>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
    // GET users/user_id
        var external_id = 1111;
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
            url: 'https://api2.socialandloyal.com/users/external_id/' + external_id + '/movements',
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

> Respuesta:

```javascript
	{"data":
    	[
        	{"type":"Promocode",
        	"desc":"Código usado: 1234",
        	"points":"25",
        	"added":"2014-01-29 13:32:42"
          },
        	{"type":"Pointget",
        	"desc":"Visita nuestra web y consigue puntos",
        	"points":"20",
        	"added":"2014-01-29 13:32:25"
          },
        	{"type":"FbRequest",
        	"desc":"Amigo invitado",
        	"points":"7",
        	"added":"2014-01-22 11:20:34"
          },
        	{"type":"Pointget",
        	"desc":"Siguenos en facebook",
        	"points":"13",
        	"added":"2014-01-21 17:01:34"
          }
    	]
	}
```

Movimientos de un usuario a partir de su external_id. Si se envía como parámetros fecha/hora de inicio y fin, devuelve los movimientos en el intervalo pedido.

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
date_ini | | false |
date_end | | false |


## POST /users

```php
<?php  
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
	// POST /users
	// *************************
	$params_data = array(
            'client_name' => 'YOUR-CLIENT-NAME',
            'external_id' => 'USER_ID',
            'name'        => 'USER_NAME',
            'surname'     => 'USER_SURNAME',
            'email'       => 'USER_EMAIL',
            'birthday'    => 'USER_BIRTHDAY',
            'location'    => 'USER_LOCATION',
            'gender'      => 'USER_GENDER'
        );
	$post_data ='';
	foreach ($params_data as $k => $v) {
    	$post_data .= $k . '='.$v.'&';
	}
	rtrim($post_data, '&');
	$url = 'https://api2.socialandloyal.com/users';
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($ch, CURLOPT_POST, count($post_data));
	curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
	$result = curl_exec($ch);
	curl_close($ch);

	echo "RESULT\n======\n". $result."\n\n";
	var_dump(json_decode($result, true));
?>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
        var timestamp = $.now();
        var params_data = {
            'client_name': 'YOUR_CLIENT_NAME'
            'external_id': 'USER_ID',
            'name':        'USER_NAME',
            'surname':     'USER_SURNAME',
            'email':       'USER_EMAIL',
            'birthday':    'USER_BIRTHDAY',
            'location':    'USER_LOCATION',
            'gender':      'USER_GENDER'
        };

        var getHMAC = function(key, timestamp) {
            var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
            return hash.toString();
        };

        // POST users
        $.ajax({
            type: 'POST',
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

> Respuesta:

```javascript
	{
    	"data":{
        	"external_id":"1845697",
        	"client_id":"26",
        	"edited":"2014-06-19 09:34:26",
        	"added":"2014-06-19 09:34:26",
        	"id":"17709"
    	}
	}
```

Añade un usuario a la base de datos de S&L

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
external_id | | true | Identificador del usuario en la base de datos del cliente
name | | false |
surname | | false |
email | | false |
birthday | | false | yyyy-mm-dd
location | | false |
gender | | false | Ejemplo: male/female
lang | | false | Ejemplo: es_es, en_us
ID campo variable | | false |

## PUT /users/external_id/{external_id}

```php
<?php  
  	$external_id = 1111;
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
  	// *************************
  	// POST /users
  	// *************************
  	$params_data = array(
              'client_name' => 'YOUR-CLIENT-NAME',
              'external_id' => 'USER_ID',
              'name'        => 'USER_NAME',
              'surname'     => 'USER_SURNAME',
              'email'       => 'USER_EMAIL',
              'birthday'    => 'USER_BIRTHDAY',
              'location'    => 'USER_LOCATION',
              'gender'      => 'USER_GENDER'
          );
  	$post_data ='';
  	foreach ($params_data as $k => $v) {
      	$post_data .= $k . '='.$v.'&';
  	}
  	rtrim($post_data, '&');
  	$url = "https://api2.socialandloyal.com/users/external_id/$external_id";
  	$ch = curl_init($url);
  	curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
  	curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
  	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
  	curl_setopt($ch, CURLOPT_POST, count($post_data));
  	curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
  	$result = curl_exec($ch);
  	curl_close($ch);

  	echo "RESULT\n======\n". $result."\n\n";
  	var_dump(json_decode($result, true));
?>
```

```javascript
  	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
  	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
  	<script type="text/javascript" >
          var external_id = 1111;
          var timestamp = $.now();
          var params_data = {
              'client_name': 'YOUR_CLIENT_NAME'
              'external_id': 'USER_ID',
              'name':        'USER_NAME',
              'surname':     'USER_SURNAME',
              'email':       'USER_EMAIL',
              'birthday':    'USER_BIRTHDAY',
              'location':    'USER_LOCATION',
              'gender':      'USER_GENDER'
          };

          var getHMAC = function(key, timestamp) {
              var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
              return hash.toString();
          };

          // PUT users
          $.ajax({
              type: 'PUT',
              url: 'https://api2.socialandloyal.com/users/external_id/' + external_id,
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

> Respuesta:

```javascript
  	{
      	"data":
      	{
          	"id":"32256",
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

Permite añadir/modificar datos de un usuario a partir de su external_id

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
external_id | | true | Identificador del usuario en la base de datos del cliente
name | | false |
surname | | false |
email | | false |
birthday | | false | yyyy-mm-dd
gender | | false |
location | | false |

## POST /users/{external_id}/external_transactions

```php
<?php  
	$external_id = 1111;
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
	// *************************
	// POST /users
	// *************************
	$params_data = array(
            'client_name'    => 'YOUR-CLIENT-NAME',
            'transaction_id' => YOUR_TRANSACTION_ID,
            'txt'            => 'Text transaction',
            'points'         => 1000,
            'price'          => 10
        );
	$post_data ='';
	foreach ($params_data as $k => $v) {
    	$post_data .= $k . '='.$v.'&';
	}
	rtrim($post_data, '&');
	$url = "https://api2.socialandloyal.com/users/$external_id/external_transactions";
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($ch, CURLOPT_POST, count($post_data));
	curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
	$result = curl_exec($ch);
	curl_close($ch);

	echo "RESULT\n======\n". $result."\n\n";
	var_dump(json_decode($result, true));
?>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
        var external_id = 1111;
        var timestamp = $.now();
        var params_data = {
            'client_name':    'YOUR_CLIENT_NAME'
            'transaction_id': 1234,
            'txt':            'Transaction test',
            'points':         1000,
            'price':           100
        };

        var getHMAC = function(key, timestamp) {
            var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
            return hash.toString();
        };

        $.ajax({
            type: 'POST',
            url: 'https://api2.socialandloyal.com/users/' + external_id + '/external_transactions',
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

> Respuesta:

```javascript
	{
    	"data":
    	{
        	"transaction_id":"1234",
        	"txt":"Transaction test",
        	"points":"1000",
        	"price":"100",
        	"user_id":"97876",
        	"external_id":"1111",
        	"edited":"2014-02-25 15:36:35",
        	"id":"3325"
    	}
	}  
```

Se añade una transacción de cliente a S&L (Disponible sólo para clientes con acceso por token).

Permite restar puntos al usuario pasando el parámetro points en negativo.

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name |  | true |
external_id |   | true | Identificador del usuario en la base de datos del cliente
transaction_id |   | true | Identificador de la transacción en la base de datos del cliente
txt |  | true |
points | |  true
price | | false

## GET /widgetToken

```php
<?php
	$public_key = 'YOUR-PUBLIC-KEY';
	$private_key = 'YOUR-PRIVATE-KEY';
	$params_data = array(
        'client_name' => 'YOUR-CLIENT-NAME',
        'external_id' => 'USER_EXTERNAL_ID'
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
	$url = 'https://api2.socialandloyal.com/widgetToken?'.$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
?>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
        // GET widgetToken
        var timestamp = $.now();
        var params_data = {
            'client_name': 'YOUR_CLIENT_NAME'
            'external_id': 'USER_EXTERNAL_ID'
        };

        var getHMAC = function(key, timestamp) {
            var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
            return hash.toString();
        };

        // GET users
        $.ajax({
            type: 'GET',
            url: 'https://api2.socialandloyal.com/widgetToken',
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

> Respuesta:

```javascript
	{
    	"data":"uMJRZEZgYtCeJ6MLacpp4kSpI5UlwLHpXIqwruvu+TY="
	}
```

Devuelve un token de acceso para un external_id de usuario (Sólo disponible para clientes con este tipo de acceso)

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
external_id | | true | Identificador del usuario en la base de datos del cliente

# Encuestas

## GET /surveys/{survey_id}

```php
<?php
	$survey_id = 17194;
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
	$url = "https://api2.socialandloyal.com/surveys/$survey_id?".$params;
	$ch = curl_init($url);
	curl_setopt($ch, CURLOPT_HTTPHEADER,$headers);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);

	$result = curl_exec($ch);
	curl_close($ch);
	echo "Result\n======\n".print_r($result, true)."\n\n";
!>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
        // GET surveys
        var survey_id = 17194;
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
            url: 'https://api2.socialandloyal.com/surveys' + survey_id,
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

> Respuesta para encuesta normal:

```javascript
	{
    	"data":[
        	{
        	"user_id":"17194",
        	"answers":[{
            	"question":"pregunta 1 selección",
            	"answer":"respuesta1"},
            	{"question":"pregunta tipo respuesta libre",
            	"answer":"e3e23"
            	},
            	{"question":"Pregunta subir imagen",
            	"answer":"https://static-sal.s3-eu-west-1.amazonaws.com/img/users/surveys/26/26_52e1544f6165c.jpg"
            	},
            	{"question":"Respuesta INPUT",
            	"answer":"3e3eee3"
            	},
            	{"question":"PREGUNTA PARA RESPUESTA TEXTAREA",
            	"answer":"e3e32e3e2"}],
        	"added":"2014-01-23 17:41:50"}
    	]
	}
```

> Respuesta para Quiz:

```javascript
	{
    "data":[{
      	"user_id":"17360",
      	"attempt":"2",
      	"result":0,
      	"answers":[{
          	"question":"pregunta1",
          	"answer":"respuesta1-2quiz"},
          	{
          	"question":"cuantos años tiene fidel castro?",
      	    "answer":"100"}],
        "added":"2014-02-12 12:09:51"}]
	}
```

Resultados de una encuesta. Por defecto no devuelve datos (respuestas) de superusuarios. Si se envía el parámetro admin_data = 1 entonces sí los devuelve todos.

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
admin_data | | false |

# Promocodes

##POST /promocodes

```php
<?php  
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
            'client_name' => 'YOUR_CLIENT_NAME'
            'name'        => 'PROMOCODE_NAME',
            'points'      => 100,
            'uses'        => 10,
            'qty'         => 10
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
?>
```

```javascript
	<script> type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.6.2/jquery.min.js"></script>
	<script> src="https://crypto-js.googlecode.com/svn/tags/3.1.2/build/rollups/hmac-sha1.js"></script>
	<script type="text/javascript" >
        var timestamp = $.now();
        var params_data = {
            'client_name': 'YOUR_CLIENT_NAME'
            'name':        'PROMOCODE_NAME',
            'points':      100,
            'uses':        10,
            'qty':         10
        };

        var getHMAC = function(key, timestamp) {
            var hash = CryptoJS.HmacSHA1(key+timestamp, 'YOUR_API_SECRET');
            return hash.toString();
        };

        // POST users
        $.ajax({
            type: 'POST',
            url: 'https://api2.socialandloyal.com/promocodes',
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

> Respuesta:

```javascript
	{
    	"data":[
    	    "CUMNXFDK",
        	"KAJNHYXR",
        	"KSMOKCWX",
        	"CWVNGTBC",
        	 .
        	 .
        	 .
    	]
	}
```

Generar lote de promocodes

Parameter | Default | Requeriment | Description
--------- | ------- | ----------- | -----------
client_name | | true |
name | | true | Nombre del lote
points | | true | Valor en puntos de los promocodes
uses | | true | Número de usos permitidos para cada promocode
qty | | true | Número de promocodes a generar
date_ini | | false | Fecha de inicio de validez del promocode. Vacía = vigente siempre
date_end | | false | Fecha de fin de validez del promocode. Vacía = vigente siempre
