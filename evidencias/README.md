# Festival Control · Bloque 01
 
## Qué he construido
He preparado una función HTTP `hello` en TypeScript para responder a `GET /dev/hello` en local. Serverless Offline recibe la petición y ejecuta `dist/hello.handler`, que devuelve el estado 200 y el JSON `{"message":"Festival Control API online"}`. No he desplegado recursos en AWS.
 
## Qué he comprendido
El handler recibe un `APIGatewayProxyEvent` y devuelve un `APIGatewayProxyResult`. `npm run build` ejecuta TypeScript (`tsc`) y genera `dist/hello.js` a partir de `src/hello.ts`; no debo editar el archivo compilado a mano. Serverless Offline ejecuta ese handler compilado. Los mensajes de `console.log` y los registros de invocación aparecen en la terminal donde está corriendo Offline.
 
## Qué he modificado
En este primer ejercicio no he cambiado el contenido del JSON: la respuesta contiene el mensaje `Festival Control API online`. La ruta sigue siendo `GET /dev/hello` porque la URL la declara la configuración de Serverless; cambiar el cuerpo de la respuesta no cambia la ruta.
 
## Cómo lo he comprobado
He comprobado la función con Thunder y con `curl -i http://localhost:3000/dev/hello`. Ambos clientes reciben HTTP 200 y el mismo JSON esperado. La terminal del backend muestra una nueva invocación al hacer la petición con curl. Evidencia propia: pendiente de adjuntar una captura o transcripción de la prueba.

# Bloque 02
 
## Resultado
- Qué añadí al proyecto: Añadí el handler `getEvent` en `src/getEvent.ts` y lo registré en `serverless.yml`, conservando la función `hello`. La ruta usa el parámetro dinámico `{id}`.
- Ruta o pantalla comprobada: `GET /dev/events/EVT-205`, probada con curl y Thunder Client.
- Estado y respuesta observados: HTTP 200 y un JSON con `"id": "EVT-205"` y los datos simulados del evento.
 
## Comprensión
- Recorrido del dato, explicado con mis palabras: El cliente envía `EVT-205` en la URL. Serverless Offline lo coloca en `event.pathParameters?.id` y el handler lo usa como id en la respuesta.
- Cambio guiado y resultado: Configuré `GET events/{id}` para ejecutar `dist/getEvent.handler`. La ruta anterior `GET /dev/hello` se conserva. Al cambiar el valor del id en la URL, la respuesta devuelve ese id sin modificar el código.
- Error encontrado y cómo lo resolví: No se registraron errores durante la prueba.
 
## Evidencias
- Captura o transcripción breve, sin credenciales: `curl -i http://localhost:3000/dev/events/EVT-205` → HTTP 200; el JSON incluye `"id": "EVT-205"`. Thunder Client devolvió la misma respuesta.
- Limitaciones del ejercicio: La ficha es simulada. Cualquier id no vacío devuelve los mismos datos del evento; todavía no se consulta un catálogo o una base de datos.

 # Bloque 03
 
## Resultado
- Qué añadí al proyecto: Añadí la función `createEvent` y la ruta `POST /events` a `serverless.yml`, conservando `hello` y `getEvent`.
- Ruta o pantalla comprobada: `POST /dev/events` para crear un evento; también `GET /dev/events/EVT-NEW` para comprobar el límite de la simulación.
- Estado y respuesta observados: En la prueba GET, HTTP 200 devuelve la ficha fija de Neon Waves aunque el id sea `EVT-NEW`. El ejercicio también contempla probar un POST válido, un campo ausente y JSON mal formado; anota aquí los estados y cuerpos exactos que observaste si los registraste.
 
## Comprensión
- Recorrido del dato, explicado con mis palabras: El POST envía los datos en el cuerpo JSON. Serverless Offline dirige la petición a `createEvent.handler`; el handler puede validar el cuerpo y construir una respuesta. La configuración de la ruta no hace que el GET anterior consulte automáticamente los datos enviados.
- Cambio guiado y resultado: Registré `POST events` en `serverless.yml` con `handler: dist/createEvent.handler` y CORS. La ruta GET `events/{id}` se mantiene independiente.
- Error encontrado y cómo lo resolví: No indiqué errores concretos en la información de esta ejecución.
 
## Evidencias
- Captura o transcripción breve, sin credenciales: `curl -i http://localhost:3000/dev/events/EVT-NEW` → HTTP 200 y ficha fija Neon Waves.
- Limitaciones del ejercicio: La creación no se refleja en el GET del bloque 02: el catálogo es simulado y no hay persistencia ni búsqueda de eventos creada por POST.

# Bloque 04
 
## Resultado
- Qué añadí al proyecto: Añadí un módulo compartido `src/events/model.ts` con los tipos, la validación, las respuestas JSON con CORS y un catálogo fijo de dos eventos. Registré los handlers de listado, detalle, creación, reemplazo, actualización y borrado en `serverless.yml`.
- Ruta o pantalla comprobada: `GET /dev/events` para la lista; `GET /dev/events/{id}` para el detalle; `POST /dev/events`, `PUT /dev/events/{id}`, `PATCH /dev/events/{id}` y `DELETE /dev/events/{id}` para las operaciones de escritura. También se propone comprobar `OPTIONS /dev/events`.
- Estado y respuesta observados: La respuesta esperada de `GET /dev/events` es HTTP 200 con `EVT-101` (Neon Waves) y `EVT-102` (Midnight Session). El contrato contempla 201 para creación, 200 para operaciones correctas, 400 para entrada inválida y 404 para un evento inexistente. No anoté aquí los resultados concretos de cada petición.
 
## Comprensión
- Recorrido del dato, explicado con mis palabras: Serverless Offline relaciona cada método y ruta con su handler compilado. Los handlers usan el modelo compartido para validar y formar respuestas JSON; el parámetro de detalle viaja en la URL. El preflight OPTIONS y la cabecera `Access-Control-Allow-Origin` permiten el acceso desde un cliente web.
- Cambio guiado y resultado: Organicé los handlers en `src/events/` y añadí un catálogo fijo y validación compartida. PUT reemplaza el recurso y PATCH representa una actualización parcial, pero POST, PUT, PATCH y DELETE sólo responden: no cambian el catálogo ni persisten datos.
- Error encontrado y cómo lo resolví: No se describió ningún error concreto durante las pruebas.
 
## Evidencias
- Captura o transcripción breve, sin credenciales: Prueba de referencia: `GET http://localhost:3000/dev/events` → HTTP 200 y lista con `EVT-101` y `EVT-102`. Para el preflight se indica `OPTIONS /dev/events` con origen `http://localhost:8081` y método solicitado POST; no se adjuntó una captura o transcripción de la salida real.
- Limitaciones del ejercicio: Los dos eventos son fijos. Las operaciones de escritura no modifican el catálogo ni tienen persistencia. Los estados y cuerpos reales de cada prueba deben completarse a partir de las salidas obtenidas localmente.
 # Bloque 05
 
## Resultado
- Qué añadí al proyecto: Añadí un cliente móvil con Expo y React Native en `mobile`, con datos de dos eventos definidos localmente y una selección controlada con `useState`.
- Ruta o pantalla comprobada: Pantalla principal de Festival Control en el navegador; muestra las tarjetas de `EVT-101` y `EVT-102`.
- Estado y respuesta observados: Al pulsar «Seleccionar EVT-101», el texto de selección cambia a `EVT-101`. Al recargar, vuelve a «ninguna» y las dos tarjetas permanecen visibles.
 
## Comprensión
- Recorrido del dato, explicado con mis palabras: `App.tsx` muestra los eventos locales. Al pulsar el botón, `onPress` llama a `setSelected`, React actualiza el estado y vuelve a dibujar el texto de selección. No se hace ninguna petición al backend.
- Cambio guiado y resultado: Creé la pantalla en TSX usando componentes de React Native, estilos con `StyleSheet` y `useState` para la interacción. La selección es sólo estado de interfaz y no se conserva al recargar la página.
- Error encontrado y cómo lo resolví: No se describió ningún error durante este ejercicio.
 
## Evidencias
- Captura o transcripción breve, sin credenciales: Resultado esperado comprobado: dos tarjetas visibles; al seleccionar, `Selección: EVT-101`; tras recargar, `Selección: ninguna`. No se adjuntó una captura.
- Limitaciones del ejercicio: Los eventos son datos locales. La pantalla todavía no consulta el backend y la selección no se guarda de forma persistente.

 # Bloque 06
 
## Resultado
- Qué añadí al proyecto: Añadí `mobile/src/api.ts` para centralizar `API_URL`, las peticiones `fetch` y la validación de la respuesta, y actualicé `App.tsx` para cargar eventos del backend.
- Ruta o pantalla comprobada: `GET http://localhost:3000/dev/events`, primero con curl y después desde la pantalla de Festival Control en el navegador.
- Estado y respuesta observados: El resultado esperado es HTTP 200 con las dos actuaciones, mostradas como tarjetas. Si el backend no está disponible, la interfaz muestra un error y «GET recargar» permite volver a intentarlo. Anota los estados que observaste realmente al ejecutar ambas pruebas.
 
## Comprensión
- Recorrido del dato, explicado con mis palabras: Al montar la pantalla, `useEffect` llama a `loadEvents`; `getEvents` hace GET a `API_URL + '/events'`. Se valida el JSON antes de guardarlo en el estado y mostrar las tarjetas. `response.ok` detecta respuestas HTTP de error; `finally` termina el estado de carga.
- Cambio guiado y resultado: Reemplacé los datos locales por la respuesta HTTP y añadí estados de carga y error, además de un botón para repetir el GET. `API_URL` apunta a `http://localhost:3000/dev`; no se añaden `/events` ni una barra final en esa constante.
- Error encontrado y cómo lo resolví: El ejercicio propone detener el backend para comprobar el error recuperable y reiniciarlo para reintentar. No se indicó si se produjo o cómo se resolvió un error durante mi ejecución.
 
## Evidencias
- Captura o transcripción breve, sin credenciales: Prueba de backend indicada: `curl -i http://localhost:3000/dev/events` → se espera HTTP 200 y dos actuaciones. No se adjuntó una salida real o captura del navegador.
- Limitaciones del ejercicio: La configuración usa `localhost` para navegador y backend en el mismo ordenador. En un teléfono, `localhost` se refiere al propio teléfono, no al ordenador. La interfaz depende de que el backend esté en marcha y permita CORS.
 
 # Bloque 07
 
## Resultado
- Qué añadí al proyecto: Amplié `mobile/src/api.ts` con las funciones `createEvent`, `replaceEvent`, `updateEvent` y `deleteEvent`, y añadí a la pantalla botones para ejecutar las cuatro operaciones.
- Ruta o pantalla comprobada: Desde la pantalla de Festival Control, los botones envían `POST /dev/events`, `PUT /dev/events/EVT-101`, `PATCH /dev/events/EVT-101` y `DELETE /dev/events/EVT-102`.
- Estado y respuesta observados: La pantalla muestra el resultado JSON de cada operación. La respuesta esperada para PATCH es HTTP 200 con el id, los cambios y `operation: "UPDATED"`. No se incluyeron las salidas reales de las cuatro peticiones.
 
## Comprensión
- Recorrido del dato, explicado con mis palabras: Cada botón llama a `run`, que ejecuta su función de API. El helper compartido construye la URL y el método, serializa el body cuando corresponde, comprueba `response.ok` y devuelve el JSON o muestra el error. El estado `busy` bloquea nuevas acciones mientras se envía la petición.
- Cambio guiado y resultado: POST y PUT envían el objeto completo del evento; PATCH envía únicamente `status`; DELETE no envía body. El resultado del servidor se muestra en pantalla y no se modifica optimistamente la lista.
- Error encontrado y cómo lo resolví: No se describió ningún error concreto durante las pruebas.
 
## Evidencias
- Captura o transcripción breve, sin credenciales: Prueba de referencia: PATCH de `EVT-101` con `{"status":"CANCELLED"}` → HTTP 200 y resultado con `operation: "UPDATED"`. No se adjuntó una captura o transcripción de la ejecución real.
- Limitaciones del ejercicio: La API simula las escrituras y no persiste cambios; por eso la lista mantiene los dos eventos originales. Los estados y cuerpos reales de POST, PUT, PATCH y DELETE deben anotarse a partir de las respuestas observadas.
 
 # Bloque 08

## Resultado
- Qué añadí al proyecto: Despliegue de la API a la nube de AWS mediante Serverless Framework, empaquetando los handlers compilados de TypeScript y creando el stack de CloudFormation con las funciones Lambda correspondientes (`hello`, `getEvent`, `createEvent`, `listEvents`, `replaceEvent`, `updateEvent`, `deleteEvent`), asociadas al rol IAM `LabRole` y publicadas mediante API Gateway.
- Ruta o pantalla comprobada: Endpoint público de API Gateway en AWS: `https://jtc2rp08ij.execute-api.us-east-1.amazonaws.com/dev`, comprobando peticiones GET a `/events` y `/hello`.
- Estado y respuesta observados: Respuestas HTTP 200 con cabeceras CORS y el payload JSON esperado devuelto directamente desde la infraestructura en la nube de AWS.

## Comprensión
- Recorrido del dato, explicado con mis palabras: La petición HTTPS llega a AWS API Gateway desde Internet. API Gateway la enruta según el método y ruta a la función AWS Lambda en Node.js 22.x correspondiente, que ejecuta el código compilado con los permisos de `LabRole` y devuelve la respuesta HTTP empaquetada al cliente.
- Cambio guiado y resultado: Configuración de `serverless.yml` con variables dinámicas de entorno (`STUDENT_CODE`, `AWS_REGION` y `LAMBDA_ROLE_ARN`). Empaquetado y despliegue exitoso con Serverless Framework, obteniendo el `ServiceEndpoint` público en la región `us-east-1`.
- Error encontrado y cómo lo resolví: Conflicto en el proveedor de credenciales al estar definidas a la vez `AWS_PROFILE=academy` y las variables de sesión en terminal (`AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`), lo que provocaba que el SDK intentara usar un perfil inexistente en `~/.aws/credentials`. Se resolvió ejecutando `unset AWS_PROFILE` para forzar al SDK a usar las claves y el `AWS_SESSION_TOKEN` temporales de AWS Academy.

## Evidencias
- Captura o transcripción breve, sin credenciales: Salida de despliegue exitoso del stack `festival-control-api-user4549459-dev` con ServiceEndpoint `https://jtc2rp08ij.execute-api.us-east-1.amazonaws.com/dev`. Invocación de comprobación mediante curl devolviendo HTTP 200 y JSON correspondiente.
- Limitaciones del ejercicio: Las credenciales de sesión en AWS Academy Learner Lab son temporales y expiran cada 4 horas. La lógica de negocio en las funciones Lambda sigue respondiendo con datos en memoria, sin persistencia en una base de datos administrada como DynamoDB.

# Bloque 09

## Resultado
- Qué añadí al proyecto: Conexión de la aplicación cliente (Expo / React Native en web) con la API real en la nube de AWS, actualizando `API_URL` en `mobile/src/api.ts` con el endpoint público de API Gateway (`https://jtc2rp08ij.execute-api.us-east-1.amazonaws.com/dev`) con Serverless Offline completamente detenido.
- Ruta o pantalla comprobada: Interfaz web de Festival Control (`http://localhost:8081`) y la pestaña Network de las herramientas de desarrollador, comprobando los cinco métodos contra AWS: `GET /events`, `POST /events`, `PUT /events/EVT-101`, `PATCH /events/EVT-101` y `DELETE /events/EVT-102`.
- Estado y respuesta observados: Códigos HTTP esperados recibidos desde la nube: 200 para GET (lista de 2 eventos), 201 para POST (evento creado `EVT-NEW`), 200 para PUT, 200 para PATCH (estado `CANCELLED`, `operation: "UPDATED"`) y 200 para DELETE. El resultado JSON se visualiza formateado en la pantalla bajo los botones.

## Comprensión
- Recorrido del dato, explicado con mis palabras: Al pulsar cada botón en la aplicación web, el cliente lanza una petición `fetch` HTTPS directa al dominio `execute-api` de AWS. API Gateway gestiona la petición (incluyendo la cabecera preflight CORS), invoca la función Lambda correspondiente en la nube y devuelve la respuesta JSON al navegador. La aplicación web comprueba `response.ok`, parsea el JSON y actualiza el estado de la interfaz sin depender en ningún momento de un servidor local.
- Cambio guiado y resultado: Sustitución de la constante local `API_URL` en `mobile/src/api.ts` por el endpoint HTTPS desplegado en AWS. Comprobación de que la aplicación funciona de forma autónoma con el backend local apagado.
- Error encontrado y cómo lo resolví: Error `npm error Missing script: "web"` al intentar arrancar el cliente web desde el directorio `backend`; se resolvió cambiando a la carpeta correcta (`cd ../mobile`). Además, se corrigió una duplicación en `mobile/src/api.ts` asegurando que `API_URL` apuntase limpiamente a la URL de AWS.

## Evidencias
- Captura o transcripción breve, sin credenciales: Pestaña Network del navegador mostrando las cinco peticiones dirigidas a `https://jtc2rp08ij.execute-api.us-east-1.amazonaws.com/dev/...` con sus respectivos métodos (GET 200, POST 201, PUT 200, PATCH 200, DELETE 200). La pantalla muestra las tarjetas de eventos y el bloque JSON con el resultado de cada acción.
- Limitaciones del ejercicio: Aunque las llamadas se ejecutan en la infraestructura real de AWS, las funciones Lambda operan sobre un catálogo simulado en memoria sin base de datos (DynamoDB), por lo que al hacer GET de nuevo la lista sigue mostrando únicamente los dos eventos base.
