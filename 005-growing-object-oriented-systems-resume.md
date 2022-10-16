# Resumen: Growing Object-Oriented Systems

Construir software es dificil, hacer que escale (especialmente en tamano) es mucho mas dificil. En mi experiencia muchos de los problemas
que he enfrentado a lo largo de estos ultimos 7 anos han sido de perspectiva.

Product owners y managers por igual ven el software muchas veces como algo fijo. Una vez que escribes el codigo te olvidas de el y pasas
al siguiente pedazo de codigo. Y que pasa si tomamos la decision incorrecta a la hora de escribir el codigo? Que pasa si el diseno y la
implementacion no son tan buenas? Que pasa si tomamos las deciciones equivocadas por que no teniamos la informacion correcta en ese
momento?

Eventualmente toda esta deuda tecnica se acumula y el sistema se vuelve rigido: se vuelve dificil no solo arreglar bugs y modificar
codigo existente, sino tambien agregar nueva funcionalidad.

Los sistemas deben evolucionar, crecer y adaptarse. Lo sistemas en realidad son como plantas, como dirian los autores del libro.
Pero no solo cualquier tipo de planta, sino una planta que produce fruto.

Una planta require tierra, abono, agua y cuidados. Si una planta es tratada de la forma adecuada y todas sus necesides estan cubiertas,
no solo producira fruto, sino que lo hara por muchisimo tiempo.

Nuestro trabajo como desarrolladores de software no es producir codigo, sino entregar valor al negocio. Ese es el fruto de nuestro
software-planta. Y no solo debemos encargarnos de que la nuestro software-planta crezca de una semilla a un baobab, sino que ademas
debemos crear las condiciones para que nuestro software-planta pueda seguir evolucionando, creciendo y produciendo.

Las tecnicas que se discuten en este libro son la recopilacion de mas de 10 anos de experiencia que tienen los autores "creciendo" sistemas
orientados a objetos.

## Para quien va dirigido este libro

Este es un libro para desarrolladores de software que quieren aprender una "flavor" un poco diferente de TDD. Tener experience previa
escribiendo pruebas, programacion orientada a objetos, patrones de diseno, java y algunos conceptos de arquitectura son requeridos.

Este no es un libro para gente que no tiene experiencia con TDD o escribiendo pruebas y que quiera aprender desde cero.

La lista entera de ejemplos esta escrita en Java. Si no estan familiarizados con este o con algun lenguaje orientado objetos se las
van a ver dificiles.

## Estructura y Resumen

El libro esta divido en 5 partes: Introduccion, el proceso de TDD, un ejemplo trabajado, tdd sustentable y topicos avanzados.

El libro cuenta con un total de 24 capitulos.

### Parte 1: Introduccion

#### Capitulos Resumidos:

En el capitulo 1 los autores nos proveen con una de las mejores, sino la mejor, explicacion de TDD que he visto.

No solo eso, si no que ademas dan una lista muy concisa de las razones y beneficios que TDD proporciona durante el proceso de desarrollo.

Al final del dia las pruebas son un mecanismo de feedback que hacen posible el cambio.

En el capitulo 2: nos introducen a la forma en la que ellos utilizan TDD: los dos bucles (hablaremos de esto mucho mas adelante,
paciencia).

Finalmente, en el capitulo 3 deciden hablarnos un poquillo de las herramientas que ellos van a utilizar en los siguientes capitulos
para armar un pequeno proyecto para ejemplificar de forma practica todas sus ideas. Estas son: jMock2 y jUnit.

#### Opinion de la seccion

Para mi, esta seccion primera seccion es donde esta lo mero bueno, la carnita como dicen los nortenos, al menos nivel teorico y
conceptual.

### Parte 2: El proceso de TDD

En esta seccion los autores hablan sobre TDD y que es un buen diseno orientado a objetos. Spoiler alert: una red de objetos
desacoplados que se comunican a traves de mensajes. Si pensaron en herencia o polimorfismo no se preocupen, a mi tambien la
universidad me fallo. Nah, es broma, no pase mis materias y me sacaron alv por burro y holgazan.

#### Capitulos Resumidos

El capitulo 4 esta enfocado en como rayos le haces para empezar un proyecto usando TDD. Aqui es donde el concepto del walking
skeleton es introducido por primera vez (ya hablaremos de eso luego, paciencia).

Ya en el capitulo 5 nos hablan de como mantener el proceso de TDD en el proyecto y el rol que las pruebas que escribimos juegan
en otras partes del desarrollo del proyecto. Al final del capitulo nos introducen al concepto de escuchar a las pruebas.

El capitulo 6 contiene una muy buena explicacion de que es un sistema de software: una serie de propiedades emergentes producto
de la composicion de las propiedades y relaciones de objetos desacoplados. Keh. Si queremos cambiar el comportamiento del sistema
entonces tenemos que cambiar esta composicion. Ya sea cambiando el orden de composicion de los objetos; o introducion y/o removiendo
objetos en la composicion.

El capitlo 7 es puro consejo practico para poder conseguir un buen diseno orientado a objetos y como TDD nos puede ayudar a
conseguir esa meta. Spoiler alert: refactorizando hasta que nos sangren los dedos.

El capitulo 8 nos habla del role que juegan las pruebas de integracion en nuestro suit de pruebas. Su proposito es ayudarnos a
probar la integracion (eh, eh, eh) de codigo que esta bajo nuestro control con el codigo que no esta bajo nuestro control (aka
librerias y dependencias). Aqui es donde nos hablan de todas las maravillas de los fake objects y mocks.

#### Opinion de la seccion

Estos capitlos estan chidos, porque los autores ven a los systemas como el producto de una composicion. Muchos de los conceptos
que usan para definir que hace un buen sistema orientado a objectos tambien es aplicable a sistemas que usan otro tipo de
paradigmas.

### Parte 3: Un Ejemplo Trabajo (A Worked Example)

La tercera parte del libro es donde la cosa se pone practica. No voy a hablar de cada capitulo en particular, ya que cada capitulo es especifico del proyecto que estan construyendo. En su lugar voy a hablar a grandes rasgos de toda la seccion.

#### Seccion resumida

A lo largo de esta seccion los autores van a elegir la primera funcionalidad que quieren implementar, despues van escribir su primera prueba de aceptacion (que va a fallar) para posteriormente crear un walking skeleton para ese feature y escribir el codigo necesario para hacer que la prueba de aceptacion pase (junto con sus correspondientes pruebas unitarias).

Despues de que su primera prueba de aceptacion pase ellos simplemente van a repetir una y otra vez. Agregando un cachito de funcionalidad a la vez siguiendo el mismo proceso: seleccionar un feature, escribir prueba de aceptacion, hacer que falle, crear un walking skeleton, agregar funcionalidad y hacer que pase.

Durante todo este proceso se van a ir deteniendo a reconsiderar decisiones tecnicas que hayan tomado y a refactorizarlas cada vez que tienen un "insight" sobre el diseno o el problema que estan intentando resolver.

Lo importante de esta seccion no es la aplicacion que construyen como tal, sino el proceso que ellos siguen a lo largo de toda la seccion para construirla.

Los autores comenzaran con una pequena aplicacion en la que todo el codigo esta dentro del metodo main para posteriormente ir evolucionandola hasta llegar a una arquitectura basada en puertos y adaptadores (tambien conocida como arquitectura hexagonal).

Basicamente en esta seccion ponen en practica todo de lo que han estado hablando en las primeras 2 secciones del libro.

#### Opinion de la seccion

Esta es la seccion que, al menos para mi, es la mas pesada. Casi la mitad de la seccion es codigo, pero no cualquier codigo, sino codigo escrito en mi lenguaje menos preferido (por no decir odiado, repudiado, despreciado, aborrecido, detestado, horrible, desagradable, infame, abyecto, aborrecido, atroz y vilipendiado): Java.

Es bonito ver en practica todas las ideas de los autores finalmente materializadas y ver como ellos trabajan e iteran hasta conseguir terminar un feature completo.

### Parte 4: TDD sustentable

La prte 4 del libro es sobre como mantener un ciclo de TDD sustentable a largo plazo. Conforme los sistemas crecen en tamano tambien crecen en complejidad, pero no solo el codigo de produccion sufre de esto, tambien el codigo de pruebas.

Las tecnicas que se discuten en estos capitulos se enfocan en mejorar la calidad de las pruebas y como lidiar con problemas que nos vamos a encontrar a la hora de escribir pruebas.

#### Capitulos Resumidos

El capitulo 20 se enfoca en un concepto que los autores ya habian introducido en las primeras secciones: escuchar las pruebas.

Cuando una unidad de pruebas nada mas no se deja probar, es decir, cuando escribir una prueba para esta unidad de codigo es muy dificil en lugar de intentar usar fuerza bruta e intentar escribir la prueba si o si deberiamos detenernos y escuchar a la prueba.

Con escuchar a la prueba se refieren a que la prueba nos esta diciendo que el diseno del codigo no es bueno. Buen codigo es codigo facil de probar.

En lugar de intentar usar fuerza bruta deberiamos refactorizar el codigo y mejorar su diseno para que sea mas facil escribir la prueba.

Los autores proveen multiples ejemplos de "code smells" que hacen el proceso de escribir pruebas dificil y algunas tecnicas y soluciones.

El capitulo 21 se enfoca en los elementos que hacen que una prueba sea facil de leer y algunos consejos para escribir pruebas faciles de leer: ponerle un nombre significativo a la prueba y usar la triple A (Arrange, Act, Assert) para mantener orden y una buena estructura en nuestras pruebas.

El capitulo 22 se enfoca en los dolores de cabeza que puede ser escribir pruebas cuando tenemos que lidiar con objetos muy grandes o complejos de crear.

Los autores introducen el patron de diseno builder para la creacion de objetos complejos en este capitulo. Toman una prueba que requiere inicializar un objeto complejo y la refactorizan poco a poco hasta crear un DSL (Lenguaje de Dominio Especifico) usando el patron builder. Esto les permite inicializar un objeto complejo utilizando un nivel de abstraccion mas alto que reduce muchisimo la complejidad.

El capitulo 23 esta enfocado en herramientas que nos permiten generar un buen diagnostico a partir de nuestras pruebas. En otras palabras: que cuando una prueba falle el error tenga sentido y nos diga el por que la prueba fallo.

Los autores hacen uso de algunas herramientas que jMock2 provee precisamente para eso.

El capitulo 24 es sobre como mantener nuestras pruebas flexibles. Al enfocarnos en solo verificar codigo relevante, excepciones relevantes y resultados relevantes evitamos que nuestras pruebas se vuelvan rigidas, es decir, evitar que pequenos cambios en el codigo hagan que nuestras pruebas fallen o se rompan todo el tiempo.

#### Opinion de la seccion

Esta seccion me gusto mucho. El capitlo 21 es la cosa mas cercana que tiene el libro a una introduccion a como escribir pruebas. No porque se enfoquen en como escribir una prueba desde cero, sino en que hace una prueba buena.

El capitulo 20 para mi fue un game change. Jamas se me habia ocurrido usar un builder crear objetos completos durante las pruebas. Suena obvio una vez que lo dices en voz alta, pero el sentido comun es el menos comun de todos los sentidos.

Esta seccion es de mis favoritas, justo despues de la primera.

### Parte 5: Topicos Avanzados

La parte 5 esta enfocada en problemas que nos vamos a encontrar a la hora de escribir pruebas para codigo que hace llamadas a bases de datos o cuando usemos hilos en Java.

La neta es que si no hacemos Java podemos ignorar en su mayoria esta seccion.

### Apendices:

Los apendices A y B se meten de lleno a jMock2 y Hamcrest matcher. Lo mismo, si no hacemos Java podemos ignorar los apendices tambien.

## Opinion del libro

Este es uno de mis libros favoritos sobre TDD y testing en general. Los conceptos de este libro son simples, pero poderosos, de ahi que sean tan eficaces.

Cuando un amigo me recomendo este libro hace un ano yo estaba tratando justamente de lidiar con la complejidad inherente de tener un sistema bastante grande que habiamos empezado desde cero. No solo resolvio muchos de los problemas con los que yo estaba lidiando en ese momento sino que ademas me forzo a cambiar la forma en la que escribo mis pruebas, sino tambien la forma en la que escribo mi codigo y la forma en la que decido empezar a construir funcionalidad nueva.

Es un libro que yo recomiendo que los programadores que si escriben pruebas lean si o si. No es una guia definitiva sobre TDD o sobre pruebas de aceptacion, pero si un libro que complementa y empodera tus habilidades de testing y la forma en la que construimos, entregamos y mantenemos software.
