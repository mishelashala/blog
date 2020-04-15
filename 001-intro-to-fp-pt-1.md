# Introduccion a la programacion funcional (parte 1)

Introduccion al concepto de funcioens matematicas

## Funciones matematicas

En programación el concepto de función difiere del que se usa generalmente en matemáticas. En matemáticas una función es un mapeo de un dominio a un codominio (o rango); en programación una función es una unidad de trabajo re-utilizable.

```js
// ejemplo 1.1
function logMessage(msg) {
  console.log(msg);
}

logMessage("hello"); // > 'hello'
```

En el `ejemplo 1.1` definimos una función llamada `logMessage` con el parámetro formal `msg`. Dentro del cuerpo de la función `logMessage` lo que hacemos es ejecutar la función `console.log` y pasarle como argumento `msg`.

Esta función realiza una operación de `I/O` (entrada y salida), pero no regresa nada de forma explícita (pero sí de forma implícita). A este tipo de funciones se les conoce generalmente como procedures.

En programación funcional todas las funciones deben producir un valor de retorno. En javascript todas las funciones regresan `undefined` de forma implícita, es decir: si nosotros no regresamos nada de forma explícita (utilizando la palabra reservada return) la función regresará `undefined` de manera automática.

```js
// ejemplo 1.2
function inc(num) {
  return num + 1;
}

inc(1); // 2
```

En el `ejemplo 1.2` declaramos la función `inc` con el parámetro formal `num` y dentro del cuerpo de la función regresamos (de forma explícita) el resultado de la expresión `num + 1`.

Esta función sí podría considerarse una función matemática, debido a que recibe un número (dominio) como valor de entrada y regresa un número (codominio).

## Sistema de tipos de haskell

JavaScript es un lenguaje interpretado, con un sistema de tipos débil y dinámico. Debido a esto, no es necesario establecer el tipo de dato a la hora de definir variables o parámetros formales.

Esto tiene sus desventajas, una de ellas es que no sabemos qué valor estamos esperando (dentro del cuerpo de la función) a la hora de ejecutar una función.

Para mitigar esto vamos a utilizar comentarios con la notación del sistema de tipos de Haskell.

```js
// ejemplo 1.3
// inc :: Number -> Number
function inc(num) {
  return num + 1;
}

inc(2); // 3
```

En el `ejemplo 1.3` pusimos el comentario `inc :: Number -> Number`, esto lee "`inc` es un miembro de la relacion de numeros a numeros". En otras palabras: "`inc` es una funcion que acepta un numero como valor de entrada y regresa un numero como valor de salida".

Cabe recalcar que estos comentarios son ignorados por el interprete y que no validan los valores de entra ni de salida de la funcion. Es solo utilizado para ayudar a futuros programadores a entender mejor como funciona la funcion.

```js
// ejemplo 1.4
// logMessage :: String -> ()
function logMessage(msg) {
  console.log(msg);
}

logMessage("hello"); // > "hello"
```

En el `ejemplo 1.4` tomamos la función del `ejemplo 1.1` y le agregamos un comentario con la notación de tipos de Haskell. En este caso `logMessage` regresa `undefined` (de forma implícita) y para representarlo utilizamos un par de paréntesis (`()`).

`undefined` es un _valor_ que se utiliza para representar la ausencia de valor, es decir: la función regresa “nada”.

> Nota: a partir de aquí utilizaremos el sistema de tipos de Haskell para todas las declaraciones de funciones.

## Funciones puras

En programación funcional existe un tipo especial de funciones: las funciones puras.

Para que una función se considere pura debe cumplir con tres características:

- Estar libre de `_side effecs_`
- Estar libre de `_side behaviors_`
- Cumplir con `_referential transparency_`

**Side effects**

Un `_side effect_` (efecto secundario) es todo aquel fenómeno que puede ser observado fuera del contexto de la función después de ser ejecutada.

```js
// ejemplo 1.5
// logMessage :: String -> ()
function logMessage(msg) {
  console.log(msg);
}
```

En el `ejemplo 1.5` tenemos la función `logMessage` y como ya habíamos dicho con anterioridad esta función hace una llamada de `I/O`. Debido a que una llamada de `I/O` produce un efecto observable fuera del contexto de la función (escribir en consola) esta función no es pura.

Algunos ejemplos de `_side effects_` son:

- `I/O` (bases de datos, leer/escribir en archivos, conecciones http, etc)
- modificar el contexto en el que la funcion se ejecutó
- modificar el `_scope_` en el que la funcion se ejecutó

```js
// ejemplo 1.6
// incrementAge :: User -> ()
function incrementAge(user) {
  user.age += 1;
}

var john = { name: "john doe", age: 21 };

incrementAge(john);

john; // { name: 'john doe', age: 22 }
```

En el `ejemplo 1.6` declaramos la función `incrementAge` con el parámetro formal `user`. Dentro del cuerpo de la función incrementamos la propiedad `age` del objeto user en una unidad.
Después definimos el objeto (asignado al identificador `john`) y lo pasamos como argumento a la ejecución de la función `incrementAge`.

Posteriormente inspeccionamos el identificador `john` y notamos que el valor de la propiedad age se ha incrementado en una unidad.

Este es un efecto observable fuera del cuerpo (y scope) de la función incrementAge. Para evitar este tipo de side effects (en programación funcional) se utilizan mecanismos como la inmutabilidad: en lugar de modificar los valores directamente creamos una copia con los cambios aplicados.

```js
// ejemplo 1.7
// incrementAge :: User -> User
function incrementAge(user) {
  return {
    ...user,
    age: user.age + 1,
  };
}

var john = { name: "john doe", age: 21 };

var updatedJohn = incrementAge(john);

updatedJohn; // { name: 'john doe', age: 22 }
john; // { name: 'john doe', age: 21 }
```

En el `ejemplo 1.7` dentro del cuerpo de la función `incrementAge` en lugar de modificar directamente el valor asignado al identificador name creamos una copia (un nuevo objeto) con la propiedad `age` incrementada una unidad.

Posteriormente inspeccionamos la versión original (`john`) y la versión actualizada (`updatedJohn`) y vemos que la versión original sigue intacta y la versión actualizada del objeto tiene los cambios que queríamos aplicar. Por lo tanto, esta versión de la función incrementAge no tiene `_side effects_`.

**Side behaviors**

Los `_side behaviors_` son la contraparte de los side effects: son todos los fenómenos observables que ocurren fuera del contexto de una función que alteran el valor de retorno de una función.

```js
// ejemplo 1.8
var one = 1;

// inc :: Number -> Number
function inc(num) {
  return num + one;
}

inc(2); // 3

one = 2; // upsis

inc(2); // 4
```

En el `ejemplo 1.8` tenemos la función inc que ya utilizamos en ejemplos anteriores con un par de cambios. Primero declaramos en el `_scope_` global y le asignamos al identificador `one` al valor `1`. Después en el cuerpo de la función en lugar de regresar el resultado de la expresión `num + 1` regresamos el resultado de la expresión `num + one`.

Después de la definición de la función `inc` la ejecutamos pasándole cómo argumento `2` y obtenemos como valor de retorno `3`. Luego, cambiamos el valor del identificador `one` a `2` y volvemos a ejecutar `inc` pasándole cómo argumento el valor `2`, pero esta vez vemos que en lugar de producir el valor `3` produce el valor `4`.

Una función con side behaviors es impredecible debido a que dependemos del contexto (y momento) en el que se ejecuta.

**Referential Transparency**

Una función posee la propiedad `_referential transparency_` cuando para producir el valor de retorno solo depende de los parámetros formales y el cuerpo de la función.

Esto implica que siempre que pasemos los mismos valores de entrada siempre obtendremos el mismo valor de retorno.

```js
// ejemplo 1.9
// inc :: Number -> Number
function inc(num) {
  return num + 1;
}

inc(3); // 4
inc(3); // 4
```

En el `ejemplo 1.9` tenemos otra vez la función `inc`. Y como ya hemos dicho para producir el valor de retorno solo depende del parámetro formal `num` y de su cuerpo para producir el resultado. De forma que si ejecutamos esta función varias veces obtenemos siempre el mismo resultado.

La función `inc` no solo posee la propiedad de referential transparency, también no produce `_side effects_`, ni es susceptible a `_side behaviors_`, de manera que es una función pura.

## Modelo de susitución/evaluación

Una expresión es todo aquello que es evaluado y produce un resultado, en javascript las funciones también son expresiones.

El modelo de evaluación/sustitución (en el caso de las funciones) dicta que al ser evaluadas las llamadas a las funciones deben ser reemplazadas por su implementación y posteriormente por el valor que producen.

```js
// ejemplo 1.10
// inc :: Number -> Number
function inc(num) {
  return num + 1;
}

// mult :: (Number, Number) -> Number
function mult(a, b) {
  return a * b;
}

var result = mult(2, inc(9)); // 20
```

En el `ejemplo 1.10` tenemos la función inc de nuevo. Después tenemos la función `mult` con los parámetros formales a y b. En el cuerpo de la función `mult` regresa la multiplicación de `a` y `b`.

Posteriormente ejecutamos la función mult con `2` como primer valor de entrada y el resultado de la evaluación de `inc(9)` como segundo valor de entrada.

Si aplicamos la sustitución a la expresión `inc(9)` el código luciría de la siguiente forma:

```js
// ejemplo 1.11
var result = mult(
  2,
  (function inc(num) {
    return num + 1;
  })(9)
);
```

En el `ejemplo 1.11` reemplazamos expresión `inc(9)` por la definición e invocación. Podríamos reemplazar seguir evaluando y reemplazando expresiones hasta llegar al resultado final:

```js
// ejemplo 1.12
// reemplacemos la expresión anterior también
var result = mult(2, 10);

var result = (function mult(a, b) {
  return a * b;
})(2, 10);

// resultado final
var result = 20;
```

El resultado final de la expresión inicial es `20`. Debido a que las funciones `inc` y `mult` son puras, podemos decir que toda la expresión es pura. Y debido a que es una expresión “pura” tenemos la certeza de que el resultado de la expresión `mult(2, inc(9))` siempre será `20`. Por ende podríamos reemplazar en cualquier parte del código la expresión `mult(2, inc(9))` por el valor `20`.

Existen dos formas de aplicar el modelo de evaluación/sustitución: 1) en tiempo de compilación; 2) en tiempo de ejecución.

En tiempo de compilación son los compiladores (y/o transpiladores) los encargados de aplicar este modelo y producir una versión del código optimizada con las expresiones reemplazadas por los valores finales.

En tiempo de evaluación son los intérpretes los encargados de aplicar este modelo conforme van analizando y ejecutando el código.

> Nota: JavaScript no utiliza el modelo de sustitución en tiempo de complicación, sino en tiempo de ejecución. Existen herramientas como prepack que utilizan este modelo y nos ayudan a generar código optimizado.

El modelo de evaluación/sustitución (en tiempo de compilación) ofrecen un beneficio significativo en performance. Debido a que las ejecuciones son reemplazadas por el valor que producen nos ahorramos la ejecución de funciones, creación de stack frames, asignación de memoria, etc.

## Resumen

Como vimos: La programación funcional gira entorno a las funciones e intenta apegarse al concepto matemático de función. También vimos las propiedades de una función pura (libre de side effects, libre de side behaviors y referential transparency) y el modelo de evaluación/sustitución y sus beneficios.

## Sources

[Mostly Adecquate Guide to Functional Programming](https://mostly-adequate.gitbooks.io/mostly-adequate-guide/) — Dr Boolean
[Structure and Interpretation of Computer Programs](https://mitpress.mit.edu/sites/default/files/sicp/index.html) - MIT
[Composing Software](https://leanpub.com/composingsoftware) - Eric Elliott
