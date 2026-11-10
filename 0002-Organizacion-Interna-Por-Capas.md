---
status: accepted
date: 2026-11-10
decision-makers: Arquitectos Software Senior (ASS)
consulted: Arquitectos Software Cognitivos (ASC)
informed: Arquitecto Software Junior (ASJ)
---


# Organización interna del sistema: estilo Por Capas

## Contexto y planteamiento del problema

El sistema debe alojar tres módulos funcionales (suscripciones, pagos y estadísticas), dos bases de datos PostgreSQL separadas y la pasarela de pago externa Stripe. Hay que decidir cómo se organizan internamente.

Requisitos relacionados: RF2 a RF8.4, RD1, RD2, RD3.

## Factores de decisión

* Las funcionalidades de suscripciones (RF2 a RF4), pagos (RF5 a RF7) y estadísticas (RF8 a RF8.4) definen tres módulos con responsabilidades distintas.
* Los tres módulos comparten la misma interfaz web y la misma capa de acceso a datos.
* RD1 y RD3 exigen dos bases de datos PostgreSQL separadas, una para administradores y redactores y otra para suscriptores.
* RD2 exige Stripe como pasarela de pago, una dependencia externa que conviene aislar.

## Opciones consideradas

* Por Capas (presentación, lógica de negocio, datos y servicios externos), con un paquete por módulo en la capa de negocio
* Microservicios
* Arquitectura hexagonal

## Resultado de la decisión

Opción elegida: «Por Capas con un paquete por módulo», porque es la organización más simple que cubre los tres módulos con un único despliegue y sin comunicación remota entre ellos.

### Consecuencias

* Bueno, porque las dependencias van siempre en un solo sentido (presentación, negocio, datos) y no hay relaciones cruzadas.
* Bueno, porque el alta de una suscripción y su cobro se confirman en el mismo proceso.
* Bueno, porque las dos bases de datos (RD1, RD3) y Stripe (RD2) quedan aisladas en sus propias capas.
* Malo, porque el sistema se despliega y escala en bloque, y un módulo no se puede desplegar por separado.

## Pros y contras de las opciones

### Por Capas con un paquete por módulo

* Bueno, porque los tres módulos comparten interfaz web y acceso a datos, y cada uno cabe en un paquete de la capa de negocio sin duplicar nada.
* Bueno, porque un único despliegue evita la comunicación remota entre módulos.
* Malo, porque el sistema escala en bloque.

### Microservicios

* Bueno, porque cada módulo podría escalar y desplegarse por separado.
* Malo, porque separaría pagos y suscripciones en procesos distintos, y el alta de una suscripción y su cobro tendrían que confirmarse entre servicios.
* Malo, porque ningún requisito pide despliegue independiente.

### Arquitectura hexagonal

* Bueno, porque aísla el dominio de todo lo externo.
* Malo, porque añadiría puertos y adaptadores a los tres módulos aunque solo el de pagos depende de un sistema externo.
* Malo, porque supone más clases e interfaces de las que justifican tres módulos.

## Más información

Debate ASS-ASC: los ASS plantearon el estilo Por Capas porque los requisitos se agrupan de forma natural en responsabilidades distintas: interfaz web, módulos de suscripciones, pagos y estadísticas, bases de datos y servicios externos (Stripe). Los ASC cuestionaron si pagos y estadísticas, al tener cargas distintas, no deberían ser servicios independientes. Los ASS respondieron que ningún requisito pide un despliegue independiente y que el alta de una suscripción y su cobro deben confirmarse en el mismo proceso, por lo que separarlos añadiría comunicación remota sin necesidad. Los ASC señalaron además que las capas no definen cómo se comunica el cliente con el servidor, algo que exige RD4, por lo que esa decisión se recogió aparte en el MADR 0001. Finalmente ambos grupos acordaron emplear el estilo Por Capas con un paquete por módulo en la capa de negocio.