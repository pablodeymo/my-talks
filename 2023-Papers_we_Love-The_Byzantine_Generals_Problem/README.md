# The Byzantine Generals Problem (1982)

[Paper link](https://lamport.azurewebsites.net/pubs/byz.pdf)
[Meetup link](https://www.meetup.com/papers-we-love-buenos-aires/events/291232392/)

## Resumen

Sistemas confiables debe manejar componente con mal funcionamiento que dan información contradictoria a diferentes partes del sistema.
Esto se modela como un grupo de generales Bizantinos armados con sus tropas frente a una ciudad enemiga.

Se comunican solamente a través de un mensajero, los generales deben acordar el plan de batalla común.
Sin embargo, alguno/s puede/n ser traidores y confundir al resto.

Problema: encontrar un algoritmo que asegure que los generales leales llegan a un acuerdo.
Se muestra que si se usan solamente mensajes orales, el problema se resuelve si al menos 2/3 de los generales son leales (un traidor puede confundir a 2 leales). 
Con mensajes escritos no falsificables, el problema se puede resolver para cualquier número de generales y de traidores.

Objetivo: implementar sistemas informáticos confiables.

## Introducción

Una parte defectuosa de un sistema puede exibir un comportamiento que a veces se pasa por alto: enviar información incoherente a distintas partes del sistema.

Cada división armada de guerreros bizantinos está comandada por un general. Los generales se comunican entre sí a través de mensajeros. Luego de observar al enemigo, deben decidir un plan de acción común.
Algunos generales pueden ser traidores: evitar que los leales lleguen a un acuerdo.

Los generales deben tener un algoritmo que asegure:

A) Todos los generales leales acuerdan sobre el mismo plan de acción: los leales deben hacer lo que el algoritmo establezca (los traidores pueden hacer lo que quieran). Los leales no solamente deben llegar a un acuerdo, sino que a un plan razonable.
B) Un número bajo de traidores no deben causar que los leales adopten un mal plan.

Cada general observa a su enemigo y comunica sus observaciones a los otros: v(i), información comunicada por el i-ésimo general.
Cada general usa su método para combinar los valores v(1),...,v(n) y obtener un único plan de acción (n es el número de generales).

"A" se alcanza con que todos los generales apliquen el mismo método.
"B" se logra usando un método robusto. Por ejemplo, la decisión es atacar o retirarse: v(i) es la opinión del i-ésimo general acerca de qué es lo mejor hacer. La decisión final puede tomarse como el voto de la mayoría => un número bajo de traidores pueden afectar la decisión si la opinión estaba dividida (en este caso, ninguna opción era un mal plan).

Para satisfacer "A" se debe cumplir:
* todos los generales deben recibir la misma información v(1),...,v(n). Esto implica que los generales no necesariamente deben utilizar el valor enviar por el i-ésimo general (puede ser diferente para varios envíos).
=> Todos los generales leales usan el mismo valor de v(i).

Tenemos el siguiente requerimiento para el i-ésimo general:
* Si el i-ésimo general es leal, entonces el valor que envía debe ser usado por cada uno de los generales como el valor v(i).

Se puede restringir el problema a como un general dado envía su resultado a los otros.
Se lo reescribe en forma de cómo un comandante general envía la información a sus tenientes:

**Problema de Generales Bizantinos** Un comandante general debe enviar una orden a sus n-1 tenientes de forma tal que:
IC1: Todos los tenientes leales obedecen la misma orden.
IC2: Si el comandante general es leal, entonces cada teniente leal obedece la orden que él envía.

IC1 e IC2 se llaman "condiciones de consistencia interactivas".
Si el comandante es leal, IC1 se desprende de IC2 (el comandante no necesita ser leal).

Para resolver el problema original, el i-ésimo general envía su valor v(i) usando la solución al Problema de Generales Bizantinos para enviar la orden "use v(i) como mi valor", con los otros generales actuando como tenientes.

## Resultados de imposibilidad

El problema de los generales bizantinos parece engañosamente simple.
La dificultad es indicar por el hecho sorprendente que si los generales envían solamente mensajes orales, ninguna solución funciona a menos que más de 2/3 de los geneales sean leales. En particular, para 3 generales, no funciona ninguna solución si uno solo es traidor.

Un mensaje oral es aquel que está en control completo del emisor => un traidor puede enviar cualquier mensaje posible. Estos son los mensajes que generalmente las computadoras se envían entre sí.

Veamos que no hay solución para 3 generales con 1 traidor.
Consideramos los mensajes como dos posibilidades: "atacar: y "retirarse".

[FIGURA] Comandante leal

El comandante leal envía la orden "atacar" a 2 tenientes. Teniente 2 es traidor y le dice al 1 que recibió retirarse. Para satisfacer IC2, teniente 1 debe obedecer atacar.

[FIGURA] Comandante traidor

El comandante traidor envía la orden atacar a un "teniente" y "retirarse" al otro. El teniente 1 no sabe quién es el traidor y no puede saber qué mensaje le envió el general al teniente 2. Para el teniente 1, la situación es igual en las dos figuras. Si el traidor miente constantemente, el teniente 1 no puede distinguir las dos situaciones y debe obedecer "atacar". Por lo tanto, cuando recibe "atacar" del comandante, debe obedecer.
Sin embargo, un argumento similar muestra que el teniente 2 recibe "retirarse" del comandante. Debe obedecer por más que el teniente 1 le diga que recibió "atacar".
El teniente 2 debe obeceder "retirarse" y el teniente 1 debe obedecer "atacar", lo cual viola IC1.
=> No hay solución para 3 generales con 1 traidor.

=> podemos mostrar que no hay solución con menos de 3m+1 generales que pueda hacer frente a m traidores. Se prueba por contradicción.

(***)

**Acuerdo aproximado**

Se puede pensar que la dificultad de resolver el problema de los Generales Bizantinos proviene de la dificultad de alcanzar un acuerdo exacto. Demostramos que no es el caso, mostrando que alcanzar acuerdo aproximado es tan difícil como alcanzar acuerdo exacto.
Asumir que en lugar de acordar en un plan preciso, los generales deben acordar en la hora aproximada del ataque.
El comandante ordena la hora del ataque y requerimos que se mantengan estas condiciones:

IC1': todos los tenientes leales atacan dentro de los 10 minutos del otro.
IC2': si el comandante general es leal, entonces cada teniente leal ataca dentro de los 10 minutos de la hora dada por el comandante.

Este problema es irresoluble al menos que 2/3 de los generales sean leales.
Supongamos que el comandante quiere enviar una orden de "atacar" o "retirarse". Ordena un ataque enviando un horario de ataque a la "1:00" y ordena "retirarse" enviando la hora de ataque "2:00", usando el algoritmo asumido. Cada teniente usa el siguiente procedimiento para obtener su orden:
1) Luego de recibir la hora de ataque del comandante, el teniente hace:
    a) si la hora es 1:10 o más temprano, ataca
    b) si la hora es 1:50 o más tarde, se retira
    c) otro caso, sigue con el paso 2)
2) Pregunta al otro teniente qué decisión alcanzó en el paso 1:
   a) si el otro teniente alcanzó una decisión, entonces toma su misma deicisión.
   b) en otro caso, se retira

Se demuestra que se llega a un imposible.

La prueba para generalizar que no hay solución para menos de 3m+1 generales con m traidores es una generalización.

## Solución con Mensajes Orales

Se presenta una solución para el caso de más de 3m+1 generales.

Cada general ejecuta un algoritmo que incluye el envío de mensajes a otros generales y asumimos que los leales ejecutan correctamente el algoritmo.
La definición de **mensajes orales** incluye las siguientes presunciones que hacen al sistema de mensajes de los generales:
A1) Cada mensaje enviado se transmite correctamente.
A2) El receptor del mensaje conoce quién lo envió.
A3) La ausencia de un mensaje puede ser detectada.

A1 y A2 evitan que un traidor interfiera en la comunicación entre dos generales (A1 implica que no se pueden modificar mensajes y A2 que un traidor no puede inyectar mensajes espúreos). A3 desincentiva a un traidor fuerce una decisión con la ausencia del envío de mensajes. 
Cada general debe poder enviarle mensajes directamente a los otros generales.

Un general traidor puede decidir NO enviar mensajes. Dado que los tenientes leales deben obedecer, necesitan un comportamiento default. Por ejemplo: RETIRARSE.

Algoritmos de Mensajes Orales OM(m), para todo m no negativo, por el cual el comandante envía órdenes a n-1 tenientes. Mostramos que OM(m) resuelve el Problems de Generales Bizantinos para 3m+1 o más generales con a lo sumo m traidores. Lo describimos como tenientes "obteniendo un valor" (en lugar de siguiendo una orden).
Asumimos la función "mayoría" con la propiedad que si la mayoría de los valores v_i iguales a v, entonces mayoría(v1,...,v_n-1)=v. Se asumen dos opciones naturales para el valor mayoría:
1- el valor mayoría entre los v_i, si existe, sino el valor "retirarse".
2- la mediana de v_i, asumiendo que vienen de un conjunto ordenado.

**Algoritmo OM(0):**
1) El general envía su valor a cada teniente.
2) Cada teniente usa el valor recibido del general o usa el valor "retirarse" si no recibe valor.

**Algoritmo OM(m), m>0:**
1) El general envía su valor a cada teniente.
2) Para cada i, sea v_i el valor que recibe el teniente i del general, o sino sea "retirarse" si no recibe valor. El teniente i actúa como el general en el algoritmo OM(m-1) enviándole el valor v_i a los n-2 otros tenientes.
3) Para cada i, y cada j!=i, sea v_i el valor que el teniente i recibe del teniente j en el paso 2) o "retirarse" si no recibe valor. El teniente i usa el valor mayoría(vi,...,vn-1).

[FIGURAS MOSTRANDO EJEMPLOS]

## Solución con Mensajes firmados

Lo que hace dificil al problema de los Generales Bizantinos es que los traidores pueden mentir.
El problema se simplifica si podemos restringir esta capacidad. Una forma es permitir a los generales enviar mensajes infalsificables.
Más precisamente, a las condiciones A1-A3 le agregamos la siguiente premisa:
A4) a) La firma de un general leal no puede ser falsificada. Cualquier alteración al contenido de su mensaje firmado puede ser detectada.
    b) Cualquiera puede verificar la autenticidad de la firma del general.

No se hacen premisas sobre la firma del general traidor => puede haber falsificaciones. Una firma puede ser falsificada por otro traidor.

Existe una solución para 3 generales.
Se plantea un algoritmo que hace frente al problema para m traidores y cualquier número de generales.

El general envía una orden firmada a cada uno de los tenientes. Cada teniente agrega su firma a la orden y la envía a los otros tenientes, que agrega su firma y la envía a los otros y así sucesivamente.
=> cada teniente debe recibir efectivamente un mensaje firmado, hacer varias copias y enviar esas copias firmadas.
El algoritmo asume la función "choice" que es aplicada a un conjunto de órdenes para obtener una. Los requerimientos son:
1. Si el conjunto V consiste de un solo elemento v => choice(V) = v.
2. choice(vacío) = "retirarse"

Una posible definición es permitir que choice(V) sea la mediana de V.

**Algoritmo SM(m)**
x:i representa el mensaje firmado por el general i
v:j:i representa el valor v firmado por j, luego el valor v:j firmado por i
Cada teniente mantiene un conjunto V_i, conteniendo el conjunto de las órdenes firmadas correctamente recibidas hasta el momento (si el comandante es leal, el conjunto debe tener un sólo elemento).
No confundir las órdenes recibidas con los mensajes recibidos (puede haber varios mensajes con la misma orden).

Inicialmente V_i = vacío.
1) el general firma y envía su valor a cada teniente.
2) para cada i:
   A) si el teniente i recibe un mensaje de la forma v:0 del comandante y él no había recibido ninguna orden, entonces:
      i) asigna V_i = {v}
      ii) envía el mensaje v:0:i a cada otro teniente
   B) Si el teniente recibe un mensaje de la forma: v:0:j_i...:j_k y v no está en el conjunto:
      i) agrega v a V_i
      ii) si k < m, envía el mensaje v:0:j_i...:j_k:i a cada uno de los otros tenientes que no están en el listado.
3) Para cada i: cuando el teniente i no recibe más mensajes (se puede usar timeout o contar los mensajes recibidos), obedece la orden choice(V_i)

[FIGURA]
en este caso los tenientes pueden detectar que el general es traidor, porque su firma aparece en dos lugares.

## Caminos de comunicación faltantes

Hasta acá se asumió que cada general se puede comunicar directamente con otro.
Ahora, quitamos esa premisa y agregamos la posibilidad de barreras físicas que impongan restricción de quién puede enviar mensajes a quien.
Los generales forman un grafo finito, simple, unidireccional. Dos generales son vecinos si están unidos por un arco (FIGURA).

@@@@ TODO!

## Sistemas confiables

La única forma que conocemos para construir un sistema informático confiables es usando diferentes "procesadores", para computar el mismo resultado y luego realizar una votación de mayoría para obtener un único valor.
Esto es verdad si uno está implementand una computadora confiafle usando circuitería redundante para protegerse de fallas individuales de chips, o un sistema de misil balístico de defensa usando computación redundante para proteger de la destrucción de lugares individuales en un caso de ataque nuclear. La única diferencia es el tamaño del "procesador" replicado.

El uso de voto de la mayoría para alcanzar robustez se basa en la premisa de que todos los procesadores no-fallantes producen el mismo resultado. Esto es cierto si usan el mismo input. Sin embargo, un único dato proviene de un único componente físico (un componente que falla puede dar resultados diferentes a los procesadores). Incluso, los procesadores correctos puede obtener valores diferentes si leen el valor mientras está cambiando.

Para que el voto de la mayoría nos permita un sistema confiable, se deben satisfacer las siguientes condiciones:
1) todos los procesadores correctos deben usar el mismo input (para producir el mismo output)
2) si la unidad de entrada no falla, todos los procesadores que no fallan deben usar el valor que provee como input (para generar el mismo output).

Estas son las condiciones interactivas de consistencia IC1 e IC2, donde el "general" es la unidad que genera el input, los tenientes son los procesadores y "leales" quiere decir que no fallan.

Una tentación es tratar de circunvalar el problema con una solución de hardware. Por ejemplo, se podría tratar de asegurar que todas las unidades obtienen el mismo valor de input leyéndolo del mismo cable. Sin embargo, un input que falla podría enviar una señal marginal en el cable (que podría ser interpretada como un 1 por unos procesadores y un 0 por otros) => no hay garantía de que dos procesadores distintos obtengan el mismo valor de input de un posiblemente input que falla, excepto teniendo procesadores comunicándose entre ellos para resolver el problema de los generales bizantinos.

Por supuesto, un input que falla puede emitir valores sin sentido. Lo único que puede hacer el Problema de los Generales Bizantinos es garantizar que todos los procesadores usen el mismo valor de input. Si el input es un valor importante, entonces se debe colocar dispositivos de entrada separados para proveer valores redundantes (por ejemplo, el sistema de defensa de misiles). Sin embargo, input redundante no alcanza confiabilidad, es necesario asegurar que procesadores non-faulty usen la data redundante para producir el mismo output.

En caso de que un dispositivo de input sea non-faulty, pero entregue distintos valores porque la lectura se hace mientras la medición cambia, queremos que los procesadores non-faulty obtengan un valor de input razonable. Se puede mostrar que si las funciones "mayoría" y "choice" se toman como la función mediana, entonces el valor tomado como input por el algoritmo cae dentro de los valores de input emitidos por el dispositivo input. => los procesadores non-faulty obtendrán un valor razonable siempre que la unidad de input genere valores de entrada razonables.

El problema de aplicar las soluciones de los generales bizantinos cae principalmente en que el pasaje de mensajes asegure las premisas A1-A3:
A1) Cada mensaje enviado por un procesador non-faulty se entrega correctamente. En sistemas reales, las comunicaciones pueden fallar. Para los algoritmos de mensajes orales, la falla de comunicación entre dos procesadores es indistinguible de la falla de un procesador. Pero podemos asegurar que estos algoritmos funcionan en presencia de a lo sumo "m" fallas (del procesador o de la línea de comunicación). Si asumimos que una falla de comunicación no puede resultar en la falsificación de un mensaje firmado (una premisa razonable), entonces el algoritmo de mensaje firmado SM(m) es insensible a fallas de comunicación. Una falla en la línea de comunicación tiene el mismo efecto que eliminar la línea de comunicación: baja la conectividad del grafo de procesadores.

A2) Cada procesador puede determinar el emisor de cualquier mensaje recibido. Lo que se necesita es que un procesador que falla no pueda impersonar a uno que no falla. En la práctica, esto significa que la comunicación IPC sea sobre líneas fijas en lugar que una línea switching.

A3) Requiere que la ausencia de un mensaje pueda ser detectada. Esta ausencia puede ser detectada solamente por la falta en la llegada en un tiempo fijo (i.e. usando una convención de timeout). Esto requiere dos premisas:
1. Existe un tiempo máximo necesario para generar y transmitir el mensaje.
2. El emisor y receptor tienen relojes que están sincronizados dentro de un error fijo máximo. No es tan obvio. Se puede mostrar que esta premisa es necesaria para resolver el problema de Generales Bizantinos. Más precisamente, suponer que permitimos algoritmos en los cuales los generales toman acción solamente en las siguientes circunstancias:
   - en un tiempo fijo inicial (para todos los generales)
   - al recibir un mensaje
   - cuando un pasa tiempo random elegido 
   
[...]

Las dos premisas de arriba facilitan detectar mensajes no enviados. Sea "u" el delay máximo de transmisión y comunicación, y asumimos que los procesadores que no fallan tienen una diferencia de reloj de a lo sumo "T". Entonces, cualquier mensaje que un procesador que no falla comienza a generar a un tiempo "t", debe llegar a destino a lo sumo en "T + t + u" en el reloj del destinatario. Entonces, si el destinatario no recibió un mensaje en ese momento, puede asumir que ese mensaje no fue enviado. Esto permite calcular hasta cuándo se debe esperar por un mensaje. Por ejemplo, para el algoritmo SM(m), un procesador debe esperar hasta: T_0 + k (u + T), para cualquier mensaje que tiene k firmas, donde T_0 es el horario en el que el general comienza a ejecutar su algoritmo.
Se debe realizar una sincronización periódica de relojes.

A4) Requiere que un procesador pueda firmar un mensaje de forma que la firma de un non-faulty pueda ser falsificada. Un mensaje firmado consiste de (M, S_i(M)). La función firma debe cumplir:
a) si el procesador i es non-faulty, un procesador que falla no puede generar S_i(M) (otro procesador no puede falsificar su firma). Esto no se puede asegurar, pero se puede asegurar con una probabilidad de confianza.
b) Dado M y X, cualquier proceso puede determinar si X = S_i(M)


Hay dos casos de interés:
* **Mal funcionamiento random:** un procesador que falla random podría generar una firma correcta con una probabilidad igual a hacer la firma de forma random.
* **Inteligencia maliciosa:** e.g. un procesador con malware, operado por alguien que irrumpió en el sistema. Falsificar la firma S_i se convierte en un problema criptográfico. Es importante agregar números de secuencia (un "nonce") para evitar ataques por repetición de mensajes firmados.

## Conclusión

Se presentaron varias soluciones al problema de los generales Bizantinos, bajo varias hipótesis y se mostraron cómo pueden ser usadas para construir sistemas informáticos confiables.
Las soluciones son "caras" en cantidad de mensajes necesarios y en tiempo.
Los algoritmos OM(m) y SM(m) requieren caminos de mensajes de largo a lo sumo m+1. En otras palabras, cada teniente puede tener que esperar por mensajes generados por el general y hayan sido reenviados a través de otros m tenientes.
Los algoritmos OM(m) y SM(m) incluyen enviar hasta (n-1)(n-2)...(n-m-1) mensajes. El número de mensajes requeridos puede disminuirse combinando mensajes.

Alcanzar confiabilidad frente a un mal-funcionamiento arbitrario es un problema dificil y su solución parece ser inherentemente cara. La única manera de reducir el costo es hacer premisas acerca del tipo de falla que puede ocurrir. Por ejemplo: se asume frecuentemente que una computadora puede fallar en responder, pero nunca responder de forma incorrecta. Sin embargo, cuando se requiere extrema confiabilidad, no se pueden hacer estas presunciones, y se debe pagar el costo completo del Problema de los Generales bizantinos.
