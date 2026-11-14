---
status: accepted
date: 2026-11-14
decision-makers: Arquitectos Software Senior (ASS)
consulted: Arquitectos Software Cognitivos (ASC)
informed: Arquitecto Software Junior (ASJ)
---

# Gestión de los reintentos de un pago fallido

## Contexto y planteamiento del problema

Si un pago falla, el sistema debe permitir reintentarlo hasta un máximo de 3 veces. Hay que decidir qué parte del diseño lleva la cuenta de los reintentos y comprueba ese límite.

Requisitos relacionados: RF9.

## Factores de decisión

* RF9: en caso de que el pago falle, el sistema permite reintentarlo hasta un máximo de 3 veces.

## Opciones consideradas

* Contador de reintentos en la clase `Pago`
* Patrón State: un estado del pago por cada fase del reintento
* Contador de reintentos en el servicio de cobro del módulo de pagos

## Resultado de la decisión

Opción elegida: «Contador de reintentos en la clase `Pago`», porque RF9 fija un límite de 3 reintentos para cada pago fallido, y es el propio pago quien mejor sabe cuántos lleva.

### Consecuencias

* Bueno, porque el límite de 3 (RF9) se comprueba sobre un dato del propio pago.
* Bueno, porque cada pago conserva cuántos reintentos se han hecho, y la cuenta de unos pagos no se mezcla con la de otros.
* Malo, porque `Pago` pasa a tener, además de los datos del cobro, la responsabilidad de contar reintentos.

## Pros y contras de las opciones

### Contador de reintentos en la clase Pago

* Bueno, porque cada pago sabe cuántos reintentos lleva y se puede comprobar el límite de 3 (RF9) sin consultar a nadie más.
* Malo, porque `Pago` mezcla los datos del cobro con el control de los reintentos.

### Patrón State

* Bueno, porque hace explícitos los estados del pago (pendiente, reintentando, fallido) hasta llegar al límite de 3 (RF9).
* Malo, porque para un único límite numérico añade una clase por estado, más estructura de la que RF9 necesita.

### Contador de reintentos en el servicio de cobro

* Bueno, porque `Pago` queda simple y toda la lógica de reintento está en un solo sitio.
* Malo, porque el número de reintentos de cada pago (RF9) no queda registrado en el propio pago y solo lo conoce el servicio.

## Más información

Debate ASS-ASC: los ASS plantearon guardar un contador de reintentos en la clase `Pago`, porque RF9 impone un límite de 3 reintentos por cada pago fallido. Los ASC cuestionaron si no era mejor que el servicio de cobro llevara la cuenta, para dejar `Pago` más simple. Los ASS respondieron que entonces el número de reintentos de cada pago no quedaría en el propio pago, y solo el servicio lo sabría. Los ASC preguntaron si no convenía un patrón State para representar las fases del reintento. Los ASS explicaron que para un único límite numérico supondría una clase por estado, más estructura de la que pide RF9. Los ASC aceptaron el argumento, aunque señalaron como contrapartida que `Pago` asume una responsabilidad más. Finalmente se acordó emplear el contador en `Pago`.