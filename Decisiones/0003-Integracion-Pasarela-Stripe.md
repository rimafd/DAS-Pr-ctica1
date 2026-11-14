---
status: accepted
date: 2026-11-14
decision-makers: Arquitectos Software Senior (ASS)
consulted: Arquitectos Software Cognitivos (ASC)
informed: Arquitecto Software Junior (ASJ)
---

# Integración con la pasarela de pago Stripe

## Contexto y planteamiento del problema

El módulo de pagos debe cobrar las suscripciones a través de Stripe y saber si cada cobro ha sido aceptado o rechazado. Hay que decidir cómo se conecta el código del periódico con Stripe.

Requisitos relacionados: RD2, RF10.

## Factores de decisión

* RD2: la pasarela de pago online debe ser Stripe.
* RF10: cada cobro se ejecuta mediante la pasarela y se registra si fue aceptado o rechazado.

## Opciones consideradas

* Adapter: interfaz propia `PasarelaPago` y una clase `StripeAdapter` que la implementa
* Facade sobre la API de Stripe
* Llamadas directas a la API de Stripe desde el módulo de pagos

## Resultado de la decisión

Opción elegida: «Adapter», porque RD2 fija Stripe pero RF10 solo necesita un resultado aceptado o rechazado, que la interfaz `PasarelaPago` expone sin que el resto del sistema conozca los tipos de Stripe. El módulo de pagos usa `PasarelaPago` para ejecutar cada cobro (RF10), y cada `Pago` pertenece a una `Suscripcion`.

### Consecuencias

* Bueno, porque el resultado del cobro (RF10) llega al módulo de pagos en un formato propio y fácil de registrar.
* Bueno, porque la dependencia de Stripe (RD2) queda en una sola clase.
* Malo, porque añade una interfaz y una clase más para una sola pasarela.

## Pros y contras de las opciones

### Adapter

* Bueno, porque traduce la respuesta de Stripe a aceptado o rechazado, que es lo que RF10 pide registrar.
* Bueno, porque todo lo relacionado con Stripe (RD2) queda en una sola clase.
* Malo, porque con una sola pasarela (RD2) la interfaz parece innecesaria.

### Facade sobre la API de Stripe

* Bueno, porque simplifica las varias llamadas a la API en un único método de cobro (RF10).
* Malo, porque el módulo de pagos seguiría usando los tipos de Stripe en sus resultados (RD2).

### Llamadas directas a la API de Stripe

* Bueno, porque es lo más rápido de implementar.
* Malo, porque los tipos y errores de Stripe se extienden por el módulo de pagos al interpretar cada respuesta (RF10).

## Más información

Debate ASS-ASC: los ASS plantearon un Adapter con una interfaz propia `PasarelaPago`, para que el módulo de pagos solo trabaje con el resultado que pide RF10 (aceptado o rechazado) y no con lo que devuelve Stripe. Los ASC cuestionaron por qué hacer una interfaz si RD2 ya fija que solo se usa Stripe. Los ASS respondieron que la interfaz no está para cambiar de pasarela, sino para que el módulo de pagos reciba un resultado propio. Los ASC preguntaron entonces en qué se diferencia de un Facade, que también simplifica el uso de Stripe. Los ASS explicaron que el Facade simplifica las llamadas, pero el módulo de pagos seguiría recibiendo los tipos y errores de Stripe, mientras que el Adapter los traduce en un único punto. Los ASC preguntaron por qué no llamar directamente a la API de Stripe, que es más simple. Los ASS respondieron que entonces los tipos y errores de Stripe se repartirían por todo el módulo de pagos. Los ASC aceptaron el argumento, aunque señalaron como contrapartida que se añade una interfaz y una clase para una sola pasarela. Finalmente se acordó emplear el Adapter.
