---
status: accepted
date: 2026-11-10
decision-makers: Arquitectos Software Senior (ASS)
consulted: Arquitectos Software Cognitivos (ASC)
informed: Arquitecto Software Junior (ASJ)
---


# Estilo arquitectónico base: Cliente-Servidor con API REST

## Contexto y planteamiento del problema

El periódico digital se usa desde clientes web (PC y Tablet) que gestionan suscripciones y pagos y envían peticiones a un servidor. Hay que fijar el estilo que organiza la relación entre los clientes y el servidor.

Requisitos relacionados: RF1, RD4.

## Factores de decisión

* RF1: el sistema debe distinguir el tipo de dispositivo (PC o Tablet) desde el que se está usando.
* RD4: las peticiones se realizan mediante protocolos HTTP/REST.

## Opciones consideradas

* Cliente-Servidor con API REST
* Peer-to-Peer
* Por eventos con un broker de mensajes

## Resultado de la decisión

Opción elegida: «Cliente-Servidor con API REST», porque RD4 define una comunicación petición-respuesta sobre HTTP/REST, que es la interacción entre un cliente y un servidor, y porque cada petición HTTP llega al servidor con la información del dispositivo, lo que permite cumplir RF1.

### Consecuencias

* Bueno, porque RD4 y el estilo coinciden y no hace falta adaptar la comunicación.
* Bueno, porque el servidor recibe en cada petición los datos del dispositivo y puede distinguir PC de Tablet (RF1) sin lógica adicional en el cliente.
* Malo, porque el servidor es el punto central de fallo y de escalado.

## Pros y contras de las opciones

### Cliente-Servidor con API REST

* Bueno, porque un servidor central concentra la gestión de suscripciones, los cobros y las estadísticas.
* Bueno, porque los dos clientes (PC y Tablet) usan la misma API.
* Malo, porque el servidor es el punto central de fallo y de escalado.

### Peer-to-Peer

* Bueno, porque no hay un único punto de fallo.
* Malo, porque sin servidor central no hay un único lugar donde gestionar las suscripciones ni cobrarlas.
* Malo, porque un navegador no actúa como par con HTTP/REST (RD4).

### Por eventos con un broker de mensajes

* Bueno, porque desacopla a quien genera un suceso de quien lo consume.
* Malo, porque las peticiones del cliente (por ejemplo, el alta o la baja de una suscripción) son petición-respuesta y RD4 fija HTTP/REST, no mensajería asíncrona.
* Malo, porque obliga a desplegar y operar un broker para un único servidor.

## Más información

Debate ASS-ASC: los ASS plantearon inicialmente el estilo Por Capas. Los ASC señalaron que las capas organizan el interior del sistema pero no definen cómo se comunica el cliente con el servidor, que exige RD4. Por eso se decidió por separado: este MADR fija la comunicación cliente-servidor y el MADR 0002 la organización interna.


