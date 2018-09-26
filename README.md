Callback_Losant
===============

-	[Introducción](#introducción)


En este repositorio se muestra el proceso para realizar el callback hacia Losant, adicionalmente se mostrará la configuración necesaria para realizar el downlink utilizando Losant.

Primero necesitamos crear una cuenta en Losant en el siguiente [Link](https://accounts.losant.com/signin?) . Una vez creada nuestra cuenta, accedemos a ella y veremos el panel de "Applications".
En la pestaña de "Applications", en la esquina superior derecha, damos click en "New Application" 

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los1.png?raw=true)

Escribimos un nombre para nuestra aplicación en Losant y una descripción. Damos click en "Create Application"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los2.png?raw=true)

después de crear nuestra aplicación, nos vamos a la pestaña de "Webhooks -> Add Webhook"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los3.png?raw=true)

escribimos un nombre para nuestro nuevo Webhook y configuramos los siguientes parametros:
-	Verification: No Verification
-	Response Code: 200
-	Marcamos la opcion "Wait for reply from workflow"

hacemos click en "Create Webhook"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los4.png?raw=true)

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los5.png?raw=true)

una vez creado nuestro webhook, copiamos el link que nos aparecerá, ya que lo utilizaremos para configurar nuestro callback en el backend de Sigfox.

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los6.png?raw=true)

regresamos a nuestra cuenta del backend de Sigfox. Buscamos nuestro dispositivo y damos click en el nombre que aparece debajo de la columna "Device Type"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los7.png?raw=true)

En la columna izquierda seleccionamos "Callbacks -> New"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los8.png?raw=true)

Seleccionamos "Custom callback"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los9.png?raw=true)

Configuramos nuestro callback de acuerdo a los siguiente:

-	Type: Data-Uplink
-	Channel: URL
-	URL pattern: Pegamos el link que obtuvimos de Losant
-	HTTP Method: POST
-	Activamos la opcion "Send SNI"
-	Content type: application/json

y en el body de nuestro json ponemos las variables que deseemos enviar. En este caso se enviaran las siguientes

		{
  			"device" : "{device}",
  			"time" : "{time}",
  			"duplicate" : "{duplicate}",
  			"snr" : "{snr}",
  			"station" : "{station}",
  			"data" : "{data}",
  			"avgSnr" : "{avgSnr}",
  			"lat" : "{lat}",
  			"lng" : "{lng}",
  			"rssi" : "{rssi}",
 			"seqNumber" : "{seqNumber}"
		}

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los10.png?raw=true)

Damos click en "OK" y ya tendremos configurado nuestro callback

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los11.png?raw=true)

Regresamos a nuestra cuenta en Losant. Vamos a "Applications -> Devices -> Add device" 

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los12.png?raw=true)

damos click en "Create Blank Device"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los13.png?raw=true)

Escribimos un nombre a nuestro dispositivo. Seleccionamos "Standalone" y definimos los siguientes atributos

		- Number	temp
		-GPS String	location
		-Number		rssi

damos click en "Create Device"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los14.png?raw=true)

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los15.png?raw=true)

a continuación nos dirigimos a la pestaña "Workflows -> Create Workflow"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los16.png?raw=true)

seleccionamos "Create Cloud Workflow"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los17.png?raw=true)

escribimos un nombre y hacemos click en "Create Workflow"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los18.png?raw=true)

nos aparecerá nuestro panel del workflow

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los19.png?raw=true)

en la columna izquierda, seleccionamos el nodo "Webhook" y verificamos en los parametros que el nombre del webhook sea el nombre que le dimos a nuestro webhook

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los20.png?raw=true)

Seleccionamos y arrastramos el nodo "debug" 

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los21.png?raw=true)

Seleccionamos y arrastramos el nodo "Webhook reply". Unimos los nodos y seleccionamos el nodo "Webhook reply". En la ventana derecha de los parametros del nodo, escribimos lo siguiente
-	Response Code Template: 200
-	Reply Body: OK

Damos click en "Save & Deploy" para guardar todas las configuraciones hechas

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los22.png?raw=true)

Ahora, mandamos un mensaje con nuestro dispositivo y en la columna derecha damos click en "Debug". Cuando se reciba un mensaje, nos desplegará la información que configuramos en el callback 

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los23.png?raw=true)

una vez que ya recibimos los menssjes en Losant, agregamos un nodo de "Function" y lo cenectamos entre los nodos de "Webhook" y "Debug".
Dentro del nodo escribimos lo siguiente:

		var buf = Buffer.from(payload.data.body.data, 'hex');
		payload.data.body.temp = buf.readFloatLE();
		
Con esta función decodificamos el dato que llega en hexadecimal en formato Float32 Little-Endian y lo transformamos a su valor decimal. Damos click en "Save & Deploy"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los24.png?raw=true)

Nuevamente enviamos un mensaje con nuestro dispositivo y dentro de las variables en el recuadro de debug, nos aparecerá la nueva variable que definimos dentro de nuestra funcion

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los25.png?raw=true)

Seleccionamos un nodo "Device State" y definimos las variables de nuestro interes. En este caso se definirán las siguientes:

		-location	{{data.body.lat}},{{data.body.lng}}
		-rssi		{{data.body.rssi}}
		-temp		{{data.body.temp}}

una vez finalizado, damos click en "Save & deploy"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los26.png?raw=true)

enviamos un mensaje con nuestro dispositivo y nos dirigimos a la pestaña de "Devices -> Debug" y veremos las tres variables que definimos en nuestro nodo anterior

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los27.png?raw=true)

Una vez que ya tenemos nuestras variables de interes definidas, crearemos el dashboard. Vamos a "Dashboard -> Create Dashboard"

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los28.png?raw=true)

Le damos un nombre a nuestro dashboard y lo creamos

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los29.png?raw=true)

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los30.png?raw=true)

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los31.png?raw=true)

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los32.png?raw=true)

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los33.png?raw=true)

![dev1](https://github.com/NXTIoT/Callback_Losant/blob/master/imagenes/los34.png?raw=true)