---
status: accepted
date: 2026-11-14
decision-makers: Arquitectos Software Senior (ASS)
consulted: Arquitectos Software Cognitivos (ASC)
informed: Arquitecto Software Junior (ASJ)
---

# Modelado de las modalidades de facturación

## Contexto y planteamiento del problema

El periódico ofrece tres modalidades de pago (trimestral 70 €, semestral 130 € y anual 190 €) y cada una se cobra de forma recurrente al terminar su ciclo. Hay que decidir cómo se modelan las modalidades para obtener el importe y la duración del ciclo de cada una.

Requisitos relacionados: RF7, RF8.

## Factores de decisión

* RF7: hay tres modalidades con importes distintos (70, 130 y 190 €).
* RF8: el cobro se repite al final de cada ciclo de la modalidad.

## Opciones consideradas

* Strategy: una interfaz `EstrategiaFacturacion` con una clase por modalidad
* Una única clase `Suscripcion` que guarda la modalidad como un dato y decide según cuál sea
* Subclases de `Suscripcion`, una por modalidad

## Resultado de la decisión

Opción elegida: «Strategy», porque las tres modalidades de RF7 solo se diferencian en el importe y en la duración del ciclo, y RF8 pide repetir el cobro igual al final de cada ciclo, así que la suscripción solo tiene que preguntarle a su modalidad el importe y el ciclo, sin comprobar cuál es.

### Consecuencias

* Bueno, porque cada modalidad guarda su importe (RF7) y la duración de su ciclo (RF8) en una sola clase.
* Bueno, porque la suscripción calcula el siguiente cobro (RF8) de la misma forma para las tres modalidades.
* Malo, porque son cuatro clases (la interfaz y tres modalidades) para tres valores fijos.

## Pros y contras de las opciones

### Strategy

* Bueno, porque cada modalidad encapsula su importe (RF7) y su ciclo (RF8).
* Malo, porque es más estructura de la que necesitan tres modalidades fijas.

### Una única clase Suscripcion que decide según la modalidad

* Bueno, porque es lo más simple para tres modalidades fijas (RF7).
* Malo, porque el importe (RF7) y la duración del ciclo (RF8) de las tres modalidades quedan mezclados dentro de la propia suscripción.

### Subclases de Suscripcion

* Bueno, porque cada subclase fija su importe (RF7) y su ciclo (RF8).
* Malo, porque tres clases hijas se diferencian solo en dos valores (importe y ciclo), y esa estructura se repite en cada una.

## Más información

Debate ASS-ASC: los ASS plantearon un Strategy porque las tres modalidades (RF7) solo cambian en importe y duración del ciclo, y el cobro recurrente (RF8) se calcula igual en todas. Los ASC cuestionaron si no bastaba con una única clase `Suscripcion` que guardara la modalidad, ya que son solo tres. Los ASS respondieron que así el importe y el ciclo de las tres modalidades quedarían mezclados dentro de la suscripción, mientras que con Strategy cada modalidad lo guarda en su propia clase. Los ASC preguntaron por qué no hacer una subclase de `Suscripcion` por modalidad. Los ASS explicaron que serían tres clases casi idénticas que solo cambian en dos valores. Los ASC aceptaron el argumento y finalmente se acordó emplear Strategy.