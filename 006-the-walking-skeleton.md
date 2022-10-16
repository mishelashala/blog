# The Walking Skeleton

## Contexto

Originalmente esto iba a ser un resumen sobre Growing Object Oriented Systems - Guided by Tests, pero despues de pasar un par de horas escribiendo me di cuenta de que literalmente no solo no estaba resumiendo secciones completas del libro, si no que ademas constantemente terminaba diciendo "puedes saltarte esta parte" o "puedes saltarte esta seccion". Y no porque no aportaran nada, sino porque al ser un libro escrito en Java para gente que hace Java (u orientado a objetos) muchos de los temas estaban acoplados con Java. Es decir, fuera del contexto de Java no agregaban valor.

Asi que decidi tirar el todo lo que escribi y en lugar de hacer un resumen del libro, sus secciones y sus capitulos voy a enfocarme en compartir y ahondar en las ideas que mas me llamaron la atencion y que ademas se pueden aplicar en multiples contextos.

## El software como una planta productiva

El libro comienza con un prefacio escrito por Kent Beck (el creador de TDD). El nos habla de una analogia: el software-planta.

La premisa de esta analogia es la idea de que tenemos un perspectiva erronea de la naturaleza del software. Vemos el software como algo fijo, algo que una vez escrito no deberia cambiar. Esta perspectiva es erronea porque lo unico constante en el desarrollo de software es el cambio. Cambian las necesides, cambian los requerimientos, cambia las reglas del negocio, cambia todo.

Construir software bajo la premisa de que una vez escrito eset ya no deberia cambiar es un error garrafal. El codigo y los sistemas deben evolucionar, crecer y adaptarse. La analogia de Kent Beck nos plantea la perspectiva de que los sistemas son en realidad como plantas. Pero no cualquier tipo de planta, sino una planta productiva, una planta que produce frutos.

Una planta require tierra, abono, agua y cuidados. La planta empieza siendo una semilla, pero no se mantiene una semilla de forma indefinida. Conforme pasa el tiempo crece, cambia y se convierte en algo mas. Si una planta es cuidada y sus necesides son cubiertas, no solo producira fruto, sino que lo hara por muchisimo tiempo.

Nuestro trabajo como desarrolladores de software no es producir codigo, es entregar valor al negocio. Ese es el fruto de nuestro software-planta. Y no solo debemos encargarnos de que nuestro software-planta crezca de una semilla y se convierta en algo mas, sino que ademas debemos crear las condiciones para que nuestro software-planta pueda seguir evolucionando, creciendo y produciendo de forma indefinida.

Las tecnicas que se discuten en este libro son la recopilacion de mas de 10 anos de experiencia que tienen los autores "creciendo" sistemas orientados a objetos.

## Pruebas como una herramienta de feedback

Cuando seguimos TDD generalmente escribimos nuestra prueba antes de escribir la implementacion que va a hacer que pase esta prueba. Esta es la primer area en la que las pruebas nos proveen de feedback. En este caso nos dan feedback sobre nuestro progreso. Como sabemos que nuestra unidad de codigo esta completa? Cuando pasa todas las pruebas. Como sabemos cuando no esta completa? Cuando no pasa todas las pruebas.

Cuando ya tenemos pruebas sobre funcionalidad existente y deseamos cambiar la implementacion sin cambiar el comportamiento externo las pruebas nos proveen de feedback sobre nuestros cambios. Si las pruebas no pasan, entonces significa que alteramos el comportamiento externo del codigo.

En resumen, las pruebas nos dan feedback sobre nuestro progreso y sobre la calidad de nuestro software.

## Calidad interna vs Calidad externa

Existen dos dimensiones sobre las cuales podemos medir la calidad del software: calidad interna y calidad externa.

La calidad externa se mide con base a las necesidades del usuario, es decir, que el software sea:

- Correcto (que haga lo que se supone que debe hacer)
- Robusto (que sea resistente a fallos)
- Eficiente (que no use demasiados recursos)
- etc

La calidad interna se mide con base a las necesidades del programador, es decir, que el software sea:

- Extensible
- Modificable
- Mantenible
- etc

La calidad externa depende directamente de la calidad interna. Si nuestro software tiene mala calidad internal entonces la calidad externa (y por consiguiente la experiencia del usuario) se veran comprometidas.

## Testing y calidad

Las pruebas como dijimos, son una herramiento que nos dan feedback. Una de las dimensiones sobre las que nos dan feedback es la calidad de nuestro software. Pero como ya vimos existen dos dimensiones de calidad.

Cada tipo de prueba nos da feedback en cada dimension pero en diferente proporcion.

Las pruebas unitarias nos dan feedback sobre la calidad interna. Un requisito para que podamos escribir una prueba unitaria es que el codigo que estamos probando este bien disenado. Es decir, que tenga altos niveles de calidad interna.

Las pruebas de e2e miden la calidad externa debido a que prueban el sistema en ambientes lo mas parecido a los ambientes de produccion.

Las pruebas de integracion estan en un punto medio. Nos dan feedback sobre ambas, la calidad externa e interna, pero en menor medida de lo que lo harian las otras dos.

## White box testing vs black box testing

Hay dos conceptos del que se habla muy poco en el libro pero que me parece importante recalcar. White box testing and black box testing.

Cuando estamos escribiendo y corriendo pruebas unitarias estamos haciendo un tipo de testing que los gringos llaman white box testing. Es un termino que se utiliza para describir cuando estamos escribiendo pruebas para un comportamiento en el sistema y tenemos acceso al codigo. Este es el tipo de testing que generalmente hacemos cuando queremos medir o mejorar la calidad interna de un sistema.

Por el contrario cuando estamos escribiendo y corriendo pruebas e2e estamos haciendo black box testing. En este caso estamos probando un comportamiento de un sistema pero en el que no tenemos acceso al codigo fuente. Este es el tipo de testing que generalmente hacemos cuando queremos medir o mejorar la calidad externa de un sistema.

## Los dos bucles

Hasta aqui ya vimos que las pruebas son un mecanismo de feedback, que diferentes tipos de pruebas miden diferentes dimensiones de calidad en nuestro sistema, lo que aun no hemos visto es la estrategia.

Siempre que se habla de escribir pruebas la piramide de las pruebas siempre es mencionada. Bien, los autores no solo no mencionan la piramide, sino que ademas nos introducen a un concepto completamente diferente. El concepto de los dos bucles.

En lugar de ver nuestra suit de pruebas como una piramide podemos verla como dos bucles. Uno grande y uno pequeno.

El bucle pequeno es el bucle de TDD que conocemos de toda la vida: escribir una prueba unitaria que falle, despues escribir el codigo que haga que pase la prueba y finalmente refactorizar. Lo que los gringos llaman el red, green and blue.

Pero este bucle pequeno a su vez forma parte de un bucle mas grande que funciona exactamente igual. Solo que en lugar de escribir una prueba unitara escribimos una prueba de aceptacion e2e.

Este approach funciona de forma top down, de arriba hacia abajo. Primero escribimos una prueba e2e de aceptacion, corremos la prueba para ver que falle y despues comenzamos a escribir el codigo necesario para hacer que la prueba pase, pero, y aqui es donde esta lo interesante es que para poder satisfacer la prueba de integracion tenemos que escribir codigo, pero debido a que estamos siguiendo TDD no podemos escribir codigo sin haber escrito una prueba primero. De manera que pasamos estar en el bucle exterior a estar en el bucle interior. Dentro del bucle interior escribirmos nuestra prueba unitaria, corremos la prueba para ver que falle, despues escribimos el codigo minimo necesario para hacer que esta prueba pase.

No podremos salir del bucle interior hasta que todas y cada uno de las unidades que necesitemos para hacer que el feature este completo esten terminadas.

## Trabajar en slices, no en capas

Hay algo muy interesante de este approach y es el hecho de que nos obliga a trabajar en rebanadas de funcionalidad en lugar de trabajar en capas.

Hoy en dia es muy comun separar nuestro software en capas. Las tres divisiones mas comunes, al menos en aplicaciones del lado del servidor, son la capa de infraestructura, la capa de logica de negocio y la capa de persistencia.

En un sistema asi es muy comun que los features atraviesen las tres capas. Esto no es necesariamente un problema. En realidad el problema es cuando nos vemos tentados a trabajar por capas. Es decir, primero terminar todo lo relacionado con la capa de persistencia, luego terminar la capa de dominio y finalmente la capa de infraestructura. Hay quienes siguen otro orden, primero el dominio, luego la infrastructura y luego la persistencia. El orden en si no es el problema, sino el trabajar en capas.

Al trabajar en capas no solo bloqueamos el progreso sino que ademas terminamos haciendo integraciones de las capas hasta que estan completamente terminadas, en lugar de integrar desde un principio lo que necesitamos.

Al seguir el approach de los dos bucles estamos obligados trabajar en rebanadas de funcionalidad. En lugar de terminar toda la capa de persistencia para poder empezar con otra capa podemos simplemente implementar solo lo que necesitamos en la capa de persistencia para poder trabajar en la siguiente capa.

## The walking skeleton

A este proceso de trabajar en slices y crear la funcionalidad minima necesaria para hacer que nuestra prueba de aceptacion pase es lo que los autores del libro llaman el walking skeleton.

El walking skeleton no solo es la funcionalidad minima que necesitamos para que la prueba pase, sino que ademas debe incluir todo lo necesario para poder testear y deployar el sistema.

Debido a que las pruebas que estamos usando son de tipo blackbox debemos deployar el sistema a un ambiente lo mas cercano a un ambiente de produccion. Esto nos obliga a hacer el sistema deployable desde el principio del proyecto.

Si no podemos deployar el sistema, no podemos hacer que nuestra prueba de aceptacion pase.

Esto nos obliga a tomar las decisiones de infraestructure que generalmente nos veriamos tentados a posponer para despues: en donde va a correr, como vamos a persistir los datos, como va a ser nuestra estrategia de deployment.

## Just enough design

Debido a que el walking skeleton es una rebanada de funcionalidad no requiere que tomemos todas las decidiones arquitecturales y de diseno desde un principio, lo que los gringos llaman "big design up front". En su lugar solo nos obliga a tomar las deciciones que nos importan en ese momento, lo que los gringos llaman "just enough design".

El walking skeleton tambien es una fuente de feedback. Si una decision de infraestructura o arquitectural impiden que podamos crear un walking skeleton, entonces las decisiones arquitecturales o de infraestructura no son las correctas.

## Automatizacion, automatizacion, automatizacion

Debido a que vamos a estar escribiendo y corriendo pruebas de aceptacion constantemente vamos a tener que estar constantemente deployando el sistema. Esto es una gran ventaja, debido a que no hay mejor manera de saber como funciona un proceso que repetirlo muchas veces. Esto eventualmente llevara a automatizar el proceso de deploy.

Es decir, nuestro sistema no solo terminara siendo deployable desde el principio, sino que ademas el proceso estara automatizado desde el principio. Nice.

## Proceso Incremental e Iterativo

Una de las razones por las que el approach de los dos bucles y el walking skeleton son tan valiosas para mi es porque funcionan de maravilla en un ambiente agil. Al usar el walking skeleton estamos agregando funcionalidad de forma incremental. Cada vez que hacemos que una prueba de aceptacion pase tenemos entre nuestras manos un feature completo.

Y debido a que podemos medir la calidad interna y externa de nuestro sistema con las pruebas cada vez que la calidad se degrade podemos iterar en el diseno para revertir o incluso mejorar la calidad. En otras palabras nos permite tener un proceso iterativo.

## Resumiendo

No suena como mucho, pero al menos para mi fueron un game changer.

Resumiendo: el walking skeleton y los bucles nos obligan a tener un sistema que:

- es deployable desde el principio
- nos fuerza a seguir integracion temprana
- nos obliga de automatizar el deployment
- seguir un ritmo de trabajo incremental e iterativo
- disenar solo que necesitamos en el momento
- nos permite medir tanto la calidad interna como la externa
- e capaz de evolicionar con el tiempo

## Opinion del libro

Este es uno de mis libros favoritos sobre testing en general. Los conceptos de este libro son simples, pero poderosos, de ahi que sean tan eficaces.

Cuando un amigo me recomendo este libro hace un ano yo estaba tratando justamente de lidiar con la complejidad inherente de tener un sistema bastante grande que habiamos empezado desde cero. No solo resolvio muchos de los problemas con los que yo estaba lidiando en ese momento sino que ademas me forzo a cambiar la forma en la que escribo mis pruebas, sino tambien la forma en la que escribo mi codigo y la forma en la que decido empezar a construir funcionalidad nueva.

Es un libro que yo recomiendo que los programadores que escriben pruebas lean si o si. No es una guia definitiva sobre TDD o sobre pruebas de aceptacion, pero si un libro que complementa y empodera tus habilidades de testing y la forma en la que construimos, entregamos y mantenemos software.
