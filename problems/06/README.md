# 6 - Escribir mensajes

Una parte escencial en nuestra API de Saga es la posibilidad de escribir mensajes en la db.

Hyperdb provee diferentes metodos para escribir data en la db, uno de ellos es:

```javascript
  db.put(key, value, cb)
```

Como dijimos anteriormente hyperdb es una key-value database, esto quiere decir que si
ejecutamos: `.put('/test', 'hi!')` el valor quedara guardado en la key `/test`.

Vieron la `/` que agregamos a la key? `Hyperdb tiene soporte para namespaces`, similar a redis.

Podemos por ejemplo leer todos los valores relacionados a un namespace particular y sus subfolders:

```javascript
db.put('/test/1', 'hi')
db.put('/test/2', 'how are you?', () => {
  db.createReadStream('/test').pipe(printInConsole)
})

// console:
// [ Node(key=test/1, value='hi', seq=1, feed=0)) ]
// [ Node(key=test/2, value='how are you?', seq=2, feed=0)) ]
```

## Ejercicio

Definir dos nuevos metodos de instancia para Saga.

`ready -> Promise`
Metodo que retorne una promesa y que se resuelve cuando la db esta `ready`.

`writeMessage(message: string) -> Promise`
Metodo que recibe como parametro un `string` y retorna una Promise.
Pero debemos tener en cuenta algunas cosas mas:
1. La Promise se resuelve cuando se termina de escribir y debe retornar la key del mensaje.
Si la escritura falla, deberia hacer un `reject` con el error.
1. Nuestra db esta encodeada en JSON por lo que debemos escribir objetos
1. No solo debemos guardar el mensaje sino tambien información adicional sobre el mismo:
`{ key, message, username, timestamp }`
1. Los mensajes se deben guardar bajo el namespace: `messages/<messageKey>`
1. Las key deben ser unicas.

## Test

```
$ npm test 06
```

## Tips

La mejor forma de definir una key unica es utilizar algun generador de IDs. Te recomendamos
que investigues: [hyperid](https://github.com/mcollina/hyperid)
