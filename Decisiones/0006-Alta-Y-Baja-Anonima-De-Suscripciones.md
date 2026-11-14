---
status: accepted
date: 2026-11-14
decision-makers: Arquitectos Software Senior (ASS)
consulted: Arquitectos Software Cognitivos (ASC)
informed: Arquitecto Software Junior (ASJ)
---

# Alta y baja anónima en categorías de noticias

## Contexto y planteamiento del problema

Un lector puede darse de alta y de baja en una o varias categorías de noticias de forma anónima. Hay que decidir cómo se modela la relación entre las suscripciones y las categorías.

Requisitos relacionados: RF3, RF4.

## Factores de decisión

* RF3: el lector se suscribe a una o varias categorías de noticias de forma anónima.
* RF4: el lector se da de baja de una o varias categorías de noticias de forma anónima.

## Opciones consideradas

* Una `Suscripcion` anónima que guarda las categorías a las que está dada de alta
* Una suscripción independiente por cada categoría
* Observer: cada categoría mantiene la lista de suscripciones registradas

## Resultado de la decisión

Opción elegida: «Una `Suscripcion` anónima que guarda las categorías a las que está dada de alta», porque RF3 y RF4 hablan de darse de alta y de baja en una o varias categorías de una vez, y esta opción lo resuelve añadiendo o quitando categorías de una misma suscripción, sin pedir nada más.

### Consecuencias

* Bueno, porque un alta en varias categorías (RF3) es una sola suscripción con varias categorías.
* Bueno, porque una baja en una o varias categorías (RF4) solo quita categorías de esa suscripción.
* Bueno, porque la suscripción no necesita datos del lector, lo que encaja con que el alta y la baja sean anónimas (RF3, RF4).
* Malo, porque para saber qué suscripciones tiene una categoría hay que recorrer las suscripciones.

## Pros y contras de las opciones

### Una Suscripcion anónima que guarda sus categorías

* Bueno, porque una sola suscripción cubre varias categorías (RF3) y se modifica en una sola operación (RF4).
* Malo, porque la categoría no conoce a sus suscripciones.

### Una suscripción independiente por cada categoría

* Bueno, porque cada alta y cada baja afecta a un único elemento.
* Malo, porque un alta en varias categorías (RF3) crea varias suscripciones, y una baja en varias (RF4) obliga a eliminar varias.

### Observer

* Bueno, porque el alta y la baja se corresponden con registrar y quitar el registro de una suscripción en una categoría.
* Malo, porque Observer sirve para avisar a los registrados cuando la categoría cambia, y RF3 y RF4 solo piden el alta y la baja, no avisar.
* Malo, porque una suscripción a varias categorías (RF3) quedaría repetida en la lista de cada una de ellas.

## Más información

Debate ASS-ASC: los ASS plantearon que la suscripción guardara las categorías a las que está dada de alta, porque RF3 y RF4 hablan de una o varias categorías en una sola operación. Los ASC cuestionaron si no era más natural un Observer, con la categoría como sujeto y las suscripciones como observadores. Los ASS respondieron que Observer sirve para avisar a los registrados de un cambio, y ningún requisito pide avisar: solo piden el alta y la baja. Los ASC preguntaron entonces por qué no una suscripción por cada categoría. Los ASS explicaron que un alta en varias categorías crearía varias suscripciones, y una baja en varias obligaría a eliminar varias. Los ASC aceptaron el argumento, aunque señalaron como contrapartida que la categoría no conoce a sus suscripciones. Finalmente se acordó que la suscripción guarde sus categorías.