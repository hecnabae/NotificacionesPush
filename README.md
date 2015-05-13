# Notificaciones PUSH en Primefaces
## 1 Requisitos
- Dependencia Atmosphere (2.2.2)
- Configuración del servlet

## 2 Anotaciones

### @PushEndPoint
Proporciona el atributo path para definir la ruta del recurso.

```
@PushEndPoint("/chat")
```

### @Singleton
Permite crear una única instancia que actúe como punto final de push.

### @OnOpen
Debe asociarse a un método.
Se invoca cuando la conexión subyacente está lista para su uso.

```
@OnOpen
public void onOpen();

@OnOpen
public void onOpen(RemoteEndPoint r);

@OnOpen
public void onOpen(RemoteEndPoint r, EventBus e)
```

- RemoteEndPoint
  - Representa la conexión física.
  - Puede utilizarse para escribir datos al navegador.
- EventBus
  - Puede utilizarse para lanzar mensajes a uno o más RemoteEndPoint utilizando expresiones regulares.

### @OnMessage
El método OnMessage() se invocará cuando un mensaje esté listo para su entrega. Los atributos disponibles para la anotación son los siguientes:
- encoders: Listado de codificadores para codificar los valores retornados por el método anotado.
- decoders: Listado de decodificadores.

```
@OnMessage(encoders={JSONEncoder.class})
public String OnMessage(String count) {
  return count;
}
```

### @OnClose
El método OnClose() se invocará cuando el cliente se desconecte.

```
@OnClose
public void onClose();

@OnClose
public void onClose(RemoteEndPoint r);

@OnClose
public void onClose(RemoteEndPoint r, EventBus e);
```

### @PathParam
Se utiliza para parsear automáticamente una ruta y asignar tokens de la ruta a variables de clase.

```
@PushEndPoint("/somepath/{room}/{user}")
@Singleton
public class ChatResource {
  @PathParam("room")
  private String room;
  
  @PathParam("user")
  private String username;
  
  //...
}
```

## 3 API

### Clase RemoteEndPoint
Representa la conexión remota, esto es el Navegador. Una instancia de RemoteEndPoint mantiene información sobre cabeceras, queryString, cuerpo, uri, ruta y segmentos de ruta que pueden utilizarse para manipular peticiones entrantes.

### Clase EventBus
Puede abarcar multiples instancias de PushEndPoint. El bus de eventos implementa publicación/suscripción y mensajería punto a punto. Para obtener una instancia de esta clase:

```
EventBus ebus = EventBusFactory.getDefault().eventBus();
```

Una vez tenemos a nuestra disposición una instancia de EventBus, la publicación de datos a suscriptores se realiza mediante la sobrecarga de métodos "publish".
- publish(Object o). Notifica el objeto 'o' a los RemoteEndPoints conectados.
- publish(String path, Object o). Notifica el objeto a los RemoteEndPoints conectados a la ruta.
- publish(String path, Object o, Reply reply). Notifica el objeto a los RemoteEndPoints conectados a la ruta mediante una instancia de Reply.
  - Reply es una interfaz con un callback llamado 'completed(String path)' que se invoca cuando se entrega el mensaje al PushEndPoint que coincide con la ruta especificada para activar el proceso de publicación.

## 4 Socket Component
Es un componente que gestiona la conexión entre el servidor y el navegador. Un modo común de utilizar el socket es la definición de una ruta y un callback para gestionar emisiones.

```
<p:push channel="/chat" onmessage="handlePublish"/>
```

### Api de cliente
widget: Primefaces.widget.socket

| Method       | Params | Return type | Description                               | 
| ------------ | ------ | ----------- | ----------------------------------------  |
| connect(uri) | uri    | void        | Conexión con la uri dada.                 |
| push(json)   | json   | void        | Notifica datos desde el lado del cliente. |
| disconnect   | -      | void        | Desconexión del canal.                    |
