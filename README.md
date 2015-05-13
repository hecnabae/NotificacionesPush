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
