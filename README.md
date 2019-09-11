# Cambio de chip: De Vanilla JS a un Framework Front-end

Este documento es una composición de notas, experiencias y tips basadas en mi
punto de vista y camino cuando me enfrenté al reto de aprender un framework
de Front-end por primera vez. Está inspirado en las estudiantes de [Laboratoria](https://www.laboratoria.la/)
aunque considero que puede ser de ayuda para cualquier aprendiz que se encuentre
atravesando este mismo desafío.

## Dinámica

Me gustaría aclarar la estructura de este documento para que pueda ser
aprovechado de manera eficiente en base a los interes personales de las diversas
lectoras. La dinámica propuesta consta de analizar cómo realizaríamos una pequeña
aplicación con VanillaJS y cómo la convertiríamos a una SPA moderna utilizando un
framework Front-end de JS. La aplicación a ver en cuestión será un **Todo App**.
Quienes me conocen probablemente estarán aburridas de ver este ejercicio una vez
más, pero considero que muestra detalles interesantes (lógica, complejidad, uso
del lenguaje, interacción con el DOM, etc) en su implementación.

> Nota: Si te causa conflicto el leer palabras en femenino cuando _"comúnmente"_
> se expresan en masculino o te sientes excluida por este detalle, lo siento,
> no considero que sea algo excluyente, sino, todo lo contrario.

> TIP: Probablemente leas el término "Front-end framework" o similar, esto es
> porque JS es un lenguaje de programación que se puede usar tanto en el
> front-end como en el back-end y hay frameworks con múltiples propósitos. En
> este documento, solo nos enfocaremos hacia aquellos que nos ayudan a construir
> interfaces interactivas de alto rendimiento.

## La forma de Vanilla JS

Listo, imaginemos que vamos a construir un _Todo App_ con HTML, CSS y Vanilla JS.
Personalmente, me gusta empezar cualquier proyecto teniendo una idea clara de lo
que queremos lograr. Así que aquí está un ejemplo del resultado esperado:

![Todo App Demo](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/06/1466857427angular2-todo-app.gif)

En resumen, se trata de una aplicación que:

* Nos permite ingresar tareas a través de un input.
* Podemos marcar una tarea como completada.
* Podemos eliminar las tareas.
* Nos muestra la cantidad de tareas que faltan completar.

Teniendo el contexto claro, empecemos por analizar, una de las formas de llevar
a cabo esta aplicación con los conocimientos de alguien que recién está
aprendiendo sobre desarrollo web y front-end en particular.

### Base del proyecto

Ya que nos vamos a enfocar en JS, partamos del punto en el que ya hemos realizado
nuestro HTML y CSS, la estructura podría verse algo así:

```html
<!-- index.html -->
<section class="todoapp">
  <header class="header">
    <h1>Tareas</h1>
    <input class="new-todo" placeholder="¿Qué hay por hacer?" autofocus>
  </header>
  <section style="display:none" class="main">
    <ul class="todo-list"></ul>
    <footer class="footer">
      <span class="todo-count"></span>
    </footer>
  </section>
</section>
```

El boilerplate completo se puede encontrar [en este CodePen](https://codepen.io/ivandevp/pen/WNeJENZ).

Aquí podemos que tenemos el título de la app y un input para comenzar a ingresar
las tareas. Además de una sección oculta que contiene la estructura que se
mostrará en el listado de tareas.

### Pensemos la solución

Antes de iniciar a codear, sería bueno que tengamos una idea de los pasos que
necesitamos expresar a través de código.

#### Agregar una tarea

Para agregar una tarea, deberíamos capturar el texto (tarea) que ingresa el
usuario y que al presionar la tecla `Enter`(`⏎`), se muestre en forma de un
listado junto con un checkbox que pueda usar para marcarla como completado y un
ícono de eliminar cuando se haga un hover sobre la tarea agregada.

#### Completar una tarea

Una vez mostrado el listado de tareas, debemos de permitir a la usuaria dar click
en el checkbox de una tarea y mostrar una respuesta en la UI que de la percepción
de que dicha tarea fue completada (en nuestro caso, la reacción será que el texto
de la tarea aparezca tachada).

#### Eliminar una tarea

Al estar posicionando el cursor sobre una tarea del listado, un ícono de eliminar
debería aparecer y si la usuaria le da click, dicha tarea debería desaparecer de
la interface.

#### Contar las tareas restantes

En cada interacción que la usuaria realice, se debería verificar si existen
tareas sin completar para mostrar la cantidad de las mismas.

### Hagamos la magia

Teniendo en cuenta los procesos que deben suceder en nuestra aplicación,
hagámosla realidad.

Primero lo primero, partamos por agregar una tarea. Debido a que el usuario
presionará la tecla `Enter` para agregar la tarea, necesitamos estar pendiente
de dicha interacción, lo cual lo logramos a través de agregar un _listener_
(escuchador) al evento del teclado. Para esto, tenemos 3 eventos que podrían
ayudarnos: `keypress`, `keydown` o `keyup`. Los cuales nos permiten saber cuando
el usuario está presionando una tecla, cuando la tiene presionada o cuando dejó
de presionar la tecla. Para este caso, nos interesa averiguar si la tecla que ha
presionado es la de `Enter`, por lo que escogeré el evento `keydown`.

> Nota: Se puede usar cualquiera de los otros eventos para este caso en particular.

```js
// Obtenemos el elemento que queremos registrar un escuchador de eventos (input)
const input = document.querySelector('input.new-todo');

// Agregamos un escuchador del evento `keydown`
input.addEventListener('keydown', (event) => {
  // TODO: Hacer lo restante una vez lanzado el evento
});
```

Bien, el método `.addEventListener()` ejecutará el callback que le pasamos como
segundo argumento cuando el evento suceda. Sin embargo, este evento se ejecutará
cada vez que el usuario presione una tecla y en nuestro caso solo queremos hacer
nuestro proceso cuando la tecla presionada haya sido un `Enter`. Una de las
formas de detectar esto, es usando el objeto de evento que nos llega al callback
a través del parámetro que hemos idenficado como `event`. Este es un objeto que
nos da información acerca del evento que se está "disparando", entre sus
propiedades se encuentra `key` y `keyCode` que contienen los valores de la tecla
que presionamos y el código ascii de la misma, respectivamente. Podemos usar
cualquiera de estas propiedades del objeto para limitar la ejecución de nuestro
código. Para este ejemplo, usaremos `keyCode`:

```js
/** ...código anterior... */

input.addEventListener('keydown', (event) => {
  // Verificamos que la tecla presionada sea la de `Enter`
  if (event.keyCode === 13) { // 13 es el código ascii de la tecla `Enter`
    // TODO: Agregar la nueva tarea
  }
});
```

En el código anterior, vemos que hacemos una validación a través de la sentencia
`if`, en la cual, todo el código que pongamos dentro del bloque (`{}`) que sigue
a esta sentencia se ejecutará solo si la condición se cumple (es verdadera).

Ahora, necesitamos averiguar lo que el usuario escribió en el `input` y crear un
elemento a partir del mismo que podamos agregar a nuestra interfaz de usuario.
Para recuperar lo que el usuario escribió, podemos aprovechar la propiedad `value`
que todos los elementos `input` contienen y que se pueden acceder a través de la
API del DOM. Una forma de llevar esto acabo podría ser obteniendo el elemento con
un método como el que usamos para obtener dicho `input` (`document.querySelector()`),
no obstante, podemos reutilizar esta variable global que tenemos en un paso previo,
o mejor aun, para no depender de un ámbito global, podemos volver a sacar provecho
del objeto del evento que también nos da información acerca del elemento sobre
el que se está ejecutando el evento, esto lo encontramos en la propiedad `.target`.
Llevando esto a código, quedaría así:

```js
/** ... código anterior, incluyendo el evento */

// La propiedad `value  nos devolverá lo que el usuario escribió en el input
// que referenciamos a través del `event.target`
const todo = event.target.value;
```

El valor almacenado en la variable `todo` será de tipo `string` (texto), dado
que es el valor de retorno definido por la API del DOM. Con este texto, podemos
comenzar a crear los elementos visuales que agregaremos a la interfaz de usuario
a través del DOM. Para esto haremos uso del método `document.createElement()`.
Para saber qué elemento debemos crear, debemos de revisar nuestra estructura del
HTML, en nuestro caso, dentro de la sección oculta, vemos que existe una lista
desordenada (`<ul></ul>`) que tiene un atributo de clase, es aquí donde podemos
agregar las tareas nuevas, dado que el contenedor es una `ul`, podemos inferir
que un elemento apropiado sería un elemento de la lista (`<li>`). Veamos como
podemos crearla:

```js
/** ...código anterior... */

// Obtenemos el contenedor existente en el HTML
const todoContainer = document.querySelector('.todo-list');
// Creamos nuestro li y lo almacenamos en una variable
const newTodoElement = document.createElement('li');
// Agregamos una clase al elemento recientemente creado, esto para darle estilos
newTodoElement.classList.add('new-todo');
// Asignamos el texto del elemento creato al valor que almacena la variable `todo`
newTodoElement.textContent = todo;
// El elemento que creamos solo existe en JS, tenemos que agregarlo al HTML para
// que el usuario pueda visualizarlo
todoContainer.append(newTodoElement);
```

La propiedad `.classList` nos permite acceder a las clases que tiene un elemento
del DOM, con el método `.add()` agregamos una clase a dicha colección. Por su
parte, la propiedad `.textContent` nos permite acceder y/o establecer el
contenido de texto de un elemento, en este caso, lo estamos estableciendo al
valor que recuperamos previamente (lo que el usuario escribió). Por último, todo
esto sucede en la memoria de la computadora que sostiene la ejecución de nuestro
código de JS, pero eso es algo que la usuaria no ve, para que sea visible debe
de estar en el HTML, con el método `.append()` aplicado a un elemento existente
(nuestro contenedor) podemos agregar el elemento que acabamos de crear al árbol
del DOM, por ende al HTML.

Todo bien, pero si prueban su código aun no se ve la tarea cuando presionan
`Enter`. Esto es debido a que la lista de tareas se encuentran dentro de una
sección con clase `main` que tiene un estilo en línea que maneja el `display`
de la sección, inicialmente con valor `none` por lo que a pesar que nuestro
elemento se ha agregado, no se puede ver. Para solucionar esto, debemos de
cambiar el display a alguno distinto de `none`, como es una `section` y su display
por defecto al ser un elemento de bloque sería `block`, vamos a establecerlo a
dicho valor de la siguiente manera:

```js
/** ...código anterior... */

// Obtenemos el elemento con clase "main"
const mainContainer = document.querySelector('.main');
// Accedemos a la propiedad style y cambiamos el valor de la propiedad display a `block`
mainContainer.style.display = 'block';
```

La API del DOM nos provee una propiedad para interactuar con los estilos de un
elemento directamente, esta propiedad es `.style` y con una asignación (`=`)
podemos cambiar su valor.

Si volvemos a probar nuestro código, veremos que funciona! :tada:

Nuestro código imperativo quedaría así de momento:

```js
// Obtenemos el elemento que queremos registrar un escuchador de eventos (input)
const input = document.querySelector('input.new-todo');

// Agregamos un escuchador del evento `keydown`
input.addEventListener('keydown', (event) => {
  // Verificamos que la tecla presionada sea la de `Enter`
  if (event.keyCode === 13) { // 13 es el código ascii de la tecla `Enter`
    // La propiedad `value  nos devolverá lo que el usuario escribió en el input
    // que referenciamos a través del `event.target`
    const todo = event.target.value;

    // Obtenemos el contenedor existente en el HTML
    const todoContainer = document.querySelector('.todo-list');
    // Creamos nuestro li y lo almacenamos en una variable
    const newTodoElement = document.createElement('li');
    // Agregamos una clase al elemento recientemente creado, esto para darle estilos
    newTodoElement.classList.add('new-todo');
    // Asignamos el texto del elemento creato al valor que almacena la variable `todo`
    newTodoElement.textContent = todo;
    // El elemento que creamos solo existe en JS, tenemos que agregarlo al HTML para
    // que el usuario pueda visualizarlo
    todoContainer.append(newTodoElement);

    // Obtenemos el elemento con clase "main"
    const mainContainer = document.querySelector('.main');
    // Accedemos a la propiedad style y cambiamos el valor de la propiedad display a `block`
    mainContainer.style.display = 'block';
  }
});
```
