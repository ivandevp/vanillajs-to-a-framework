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

Si quieres probar como va funcionando hasta ahora, puedes ver [este CodePen](https://codepen.io/ivandevp/pen/bGbMaNR).

Wohooo!! :tada: Tenemos el primer paso listo, ahora vamos por el segundo que
trata sobre completar una tarea. Para esto, debemos de agregar el checkbox que
nos permita darle click y que de esa manera la usuaria pueda indicar que la
tarea se ha completado. Para esto, debemos de modificar un poco el código dentro
de nuestro escuchador de eventos, para que pueda crearse el checkbox en el DOM
y agregarse conjuntamente como parte del elemento de la lista de tareas. Vamos
a realizarlo usando los métodos de la API del DOM que hemos vista hasta el momento.

Para esto, me parece mejor imaginar la estructura de cada elemento de la lista,
antes de proceder y saber qué es lo que necesitamos crear, esta es una propuesta:

```html
<li class="new-todo">
  <div class="view">
    <input class="toggle" type="checkbox" />
    <label>Tarea</label>
    <button class="destroy"></button>
  </div>
</li>
```

Esta estructura nos permitirá manejar la acción de completar una tarea, así como
la de eliminarla. Cambia sustancialmente lo que nosotros creamos hasta el momento
que se ve algo así:

```html
<li class="new-todo">Tarea</li>
```

Hagamos el código que nos va a permitir crear esta estructura en el DOM:

```js
// Creamos el elemento de la lista
const newTodoElement = document.createElement('li');
// Le agregamos la clase "new-todo" al <li />
newTodoElement.classList.add('new-todo');

// Creamos el div que contendrá los elementos del elemento de la lista
const newTodoBox = document.createElement('div');
// Le agregamos la clase "view" al <div />
newTodoBox.classList.add('view');

// Creamos el checkbox que es un elemento input
const newTodoCompleteCheckbox = document.createElement('input');
// Le agregamos la clase "toggle" al <input />
newTodoCompleteCheckbox.classList.add('toggle');
// Indicamos que el input es tipo "checkbox"
newTodoCompleteCheckbox.type = 'checkbox';

// Creamos el label que mostrará el texto de la tarea que la usuaria agregó
const newTodoLabel = document.createElement('label');
// Asignamos el texto que la usuario ingresó
newTodoLabel.textContent = todo;

// Creamos el botón que permitirá eliminar una tarea
const newTodoDestroyButton = document.createElement('button');
// Le agregamos la clase "destroy" al <button />
newTodoDestroyButton.classList.add('destroy');

// Agregamos los elementos que van dentro del contenedor <div class="view" />
newTodoBox.append(newTodoCompleteCheckbox);
newTodoBox.append(newTodoLabel);
newTodoBox.append(newTodoDestroyButton);

// Agregamos el contenedor al elemento de la lista <li class="new-todo">
newTodoElement.append(newTodoBox);

// Obtenemos el contenedor existente en el HTML <ul class="todo-list" />
const todoContainer = document.querySelector('.todo-list');
// Agregamos el elemento de la lista al DOM (HTML)
todoContainer.append(newTodoElement);

// Obtenemos la sección que muestra la lista de tareas
const mainContainer = document.querySelector('.main');
// Volvemos visible la sección
mainContainer.style.display = 'block';
```

Ahora que tenemos los controles necesarios para completar y eliminar una tarea,
debemos de agregar el código que realice las acciones correspondientes. Para esto,
volveremos a usar los manejadores de eventos del DOM. Como estamos enfocados en
la acción de completar una tarea, vamos a lograr esto haciendo click en el
checkbox que está en la UI. A simple vista, esto nos da a entender que el evento
que debemos de escuchar el de `click`, y podemos lograr el resultado esperado,
pero probablemente nos agregue cierta complejidad como detectar si el estado del
checkbox al momento de hacer click es que está marcado o desmarcado. Para
evitarnos dicho proceso, podemos usar el evento `change` que se "dispara" cuando
el estado (si está marcado o no) de este checkbox cambia. Para agregarlo, podemos
hacer lo siguiente:

```js
/** ...código anterior... */

// Registramos un escuchador para el evento change del <input type="checkbox" />
newTodoCompleteCheckbox.addEventListener('change', (event) => {

});

/** ...código que prosigue... **/
```

Escuchando el evento, ahora solo debemos de hacer la opción de que se muestre
como tachado, para generar dicha reacción lo haremos a través de `css`, con la
propiedad `text-decoration: line-through;`, esta debe ser aplicada al texto de
la tarea que en código significa al label dentro del `li`. Para no agregarla y
quitarla manualmente, vamos a hacer uso de una clase que ya se encuentra como
parte del boilerplate, llamada `completed`. La clase en mención deberá aplicarse
sobre el elemento `<li></li>` que contiene la tarea. Esto implicará que recorramos
el DOM, ya que el evento se está aplicando sobre el `checkbox` y no el elemento
de la lista (`li`). Esto quedaría algo así:

```js
/** ...código anterior... */

// Registramos un escuchador para el evento change del <input type="checkbox" />
newTodoCompleteCheckbox.addEventListener('change', (event) => {
  // Obtenemos el <div class="view" />
  const divContainer = event.target.parentElement;
  // Obtenemos el <li class="new-todo" />
  const todoElement = divContainer.parentElement;

  // Agregamos o quitamos la clase "completed" del <li />
  todoElement.classList.toggle('completed');
});

/** ...código que prosigue... **/
```

Algo muy común que solemos hacer en casos como este es acceder directamente a
las variables que tenemos disponibles en un ámbito superior, por ejemplo,
si yo se que existe una variable `newTodoElement` a la cual tengo acceso y hace
referencia al elemento `<li />` que quiero usar, ¿por qué no usarlo? En palabras
simples, confiar en variables globales no es una buena idea, porque implica que
si su valor es accidentalmente cambiado, afecta a más código del que debería,
además que en este momento funcionaría porque todo nuestro código está en un mismo
lugar, sin embargo, si esto fuera algo profesional, en el proceso de code review
nos estarían pidiendo a gritos refactorizar este código (más adelante veremos
este punto). Por lo tanto, vamos a usar nuestro objeto de evento para obtener
el checkbox exacto que la usuaria click y que, por ende, su estado cambió. Como
vimos anteriormente, este lo obtenemos con `event.target`, pero nosotras
necesitamos el `<li />`, para esto, accedemos al padre del checbkox, la cual es
`<div class="view" />`, así que necesitamos acceder al padre de esto, para ambos
casos lo hacemos a través de la propiedad `.parentElement`. Una vez que logramos
acceder al elemento de la lista, necesitamos agregar la clase `completed`, pero
qué sucede si la usuaria le da click a una tarea completada, se debería quitar
la clase `completed` para que esta se vea como antes en la UI. Si pensaste en
hacer una condición (`if`), está más que genial, sin embargo, el DOM nos provee
un método para manejar clases llamado `.toggle()`, este nos permite agregar si
en caso no está agregada o eliminarla si en caso ya está agregada. Y con eso
logramos nuestro segundo paso :tada: :balloon:!!

Puedes revisar el código hasta este punto en [este pen](https://codepen.io/ivandevp/pen/wvwjXBx).

Yay! Tenemos 2 pasos de 4 completados. Vamos por el tercero, este implica darle
vida al botón de eliminar una tarea. Al igual que el paso anterior, necesitamos
agregarle una acción al botón cuando la usuario le de click, usaremos un evento
para esto:

```js
/** ...código anterior... */

// Registramos un escuchador para el evento click del <button type="destroy" />
newTodoDestroyButton.addEventListener('click', (event) => {

});

/** ...código que prosigue... **/
```

Para eliminar una tarea, literalmente necesitamos quitar el elemento `<li />` del
HTML, y esto lo logramos con los métodos de la API del DOM:

```js
/** ...código anterior... */

// Registramos un escuchador para el evento click del <button type="destroy" />
newTodoDestroyButton.addEventListener('click', (event) => {
  // Obtenemos el <div class="view" />
  const divContainer = event.target.parentElement;
  // Obtenemos el <li class="new-todo" />
  const todoElement = divContainer.parentElement;

  // Quitamos el elemento del HTML
  todoElement.remove();
});

/** ...código que prosigue... **/
```

Al igual que en el paso anterior, necesitamos acceder desde el botón que la usuaria
da click hasta el elemento de la lista (`<li />`) y haciendo uso del método
`.remove()` nos basta para quitar por completo dicho elemento de nuestro HTML.

Puedes ver el código que llevamos hasta este punto en [este CodePen](https://codepen.io/ivandevp/pen/mdbLKGG).

Por último, pero no menos importante, debemos de mostrar las tareas que están
pendientes de ser completadas. Para llevar a cabo este paso, podemos pensar de
muchas maneras y vamos a usar una forma muy simple para fines didácticos. Usemos
un contador global, :scream: lo sé, acabo de mencionar líneas arriba que variables
globales son cosas del mundo oscuro, pero estamos aprendiendo :woman_shrugging:.
Nuestro contador inicia en 0, pues no tenemos tareas hasta que la usuaria lo
agregue, cuando se agrega una tarea, nuestro contador debe incrementarse en 1;
es decir, si agregamos 2 tareas, deberíamos sumar 1 dos veces y por lo tanto
nuestro contador debería tener el valor de 2. Cada vez que marquemos como
eliminado o completado deberíamos disminuir el contador en 1. Si tenemos 2 tareas
y eliminamos o completamos 1, debemos restar 1 a nuestro contador y por lo tanto
su valor sería 1. El caso más complejo es que si hay una tarea marcada como
completada y la usuaria le vuelve a dar click, debemos de incrementar nuestro
contador y mostrar el valor correcto. Manos a la obra:

```js
let pendingTasks = 0;

/** ...código anterior... */

input.addEventListener('keydown', (event) => {
  // Verificamos que la tecla presionada sea la de `Enter`
  if (event.keyCode === 13) { // 13 es el código ascii de la tecla `Enter`
    // código de agregar una tarea

    newTodoCompleteCheckbox.addEventListener('change', (event) => {
      // código de completar una tarea

      // Si el estado del checkbox está marcado, restamos el contador
      if (event.target.checked) {
        pendingTasks -= 1;
      } else {
        // Caso contrario, aumentamos el contador
        pendingTasks += 1;
      }
    });

    newTodoDestroyButton.addEventListener('click', (event) => {
      // código de eliminar una tarea

      // Disminuimos el contador de tareas pendientes
      pendingTasks -= 1;
    });

    // Aumentamos el contador de tareas pendientes
    pendingTasks += 1;
  }
});

/** ...código que prosigue... **/
```

Además de obtener el contador correctamente, debemos de mostrarlo, para esto,
en nuestra estructura del HTML tenemos un `<span class="todo-count"></span>`, el
cual podemos modificar su texto contenido para mostrar el valor del contador.
Podemos hacerlo de la siguiente manera:

```js
let pendingTasks = 0;

/** ...código anterior... */

input.addEventListener('keydown', (event) => {
  // Verificamos que la tecla presionada sea la de `Enter`
  if (event.keyCode === 13) { // 13 es el código ascii de la tecla `Enter`
    // código de agregar una tarea

    newTodoCompleteCheckbox.addEventListener('change', (event) => {
      // código de completar una tarea

      // Si el estado del checkbox está marcado, restamos el contador
      if (event.target.checked) {
        pendingTasks -= 1;
      } else {
        // Caso contrario, aumentamos el contador
        pendingTasks += 1;
      }
      // Actualizamos el contador en la UI
      document.querySelector('.todo-count').textContent = pendingTasks;
    });

    newTodoDestroyButton.addEventListener('click', (event) => {
      // código de eliminar una tarea

      // Disminuimos el contador de tareas pendientes
      pendingTasks -= 1;
      // Actualizamos el contador en la UI
      document.querySelector('.todo-count').textContent = pendingTasks;
    });

    // Aumentamos el contador de tareas pendientes
    pendingTasks += 1;
    // Actualizamos el contador en la UI
    document.querySelector('.todo-count').textContent = pendingTasks;
  }
});

/** ...código que prosigue... **/
```

Wowww! Lo logramos :tada:!! Puedes revisar el código completo en este [CodePen](https://codepen.io/ivandevp/pen/ZEzoRdB).

> Nota: Si probaste este último paso, te darás cuenta que hay un caso que no
> funciona bien, si tienes una solución, mándame un PR con un link a tu CodePen
> donde esto funcione correctamente :wink:

Genial. Ya tenemos algo funcional, pero nos toca ser un poco críticos respecto al
código que acabamos de hacer, ciertas cosas no se ven bien:

* El código es completamente imperativo, le estamos diciendo exactamente qué
  hacer en cada línea de código.
* Todo vive en una función que se está pasando como argumento de un evento.
* No se puede testear nada de este código.
* Si volvemos a este código en un tiempo X, probablemente tengamos que leer todo
  el código para saber qué hicimos, a pesar de que está comentado prácticamente
  en cada línea (esto por fines de explicar lo que estamos haciendo, no porque
  lo necesite).

Vamos a refactorizar nuestro código, para que esté más decente:
[CodeSandbox](https://codesandbox.io/s/xenodochial-heisenberg-zxxsu?fontsize=14)*

* _En progreso, toda ayuda es bienvenida :smiley:_

> Nota: No nos vamos a enfocar en los pasos de refactorización porque van fuera
> del objetivo de este documento, pero si te interesa ver o leer el proceso,
> puedes abrir un issue solicitándolo :wink:.

## Intro a Frameworks Front-end

Comienza lo bueno! Cuando decidimos crear una aplicación web usando algún
framework o librería de front-end moderno, algunos conceptos nuevos y raros al
principio se dan a conocer. Conceptos como componentes y su ciclo de vida,
estado de la aplicación, single page apps, enrutamiento, etc.

Empecemos por definir alguno de ellos:

### Componentes

El concepto de componentes es lo más similar que personalmente encuentro a una
función en código, ya que, las funciones son unidades de código que en base a un
dato de entrada o no agrupan y realizan instrucciones, que pueden ser
reutilizables en más de una parte de tu programa y que se pueden testear de
manera individual garantizando su correcto funcionamiento. De igual manera, un
componente es la unidad de una interfaz de usuario que engloba cierto
comportamiento y representación visual que puede ser reutilizable en diferentes
pantallas y testeable de manera aislada, además, dicho comportamiento y
representación puede variar en base a datos de entrada.

Ejemplo de una función:

```js
function getNumberOfAvailableSeats(seats) {
  const availableSeats = seats.filter(seat => seat.available);
  return availableSeats.length;
}
```

En el ejemplo anterior, tenemos una función que calcula la cantidad de asientos
disponibles de un restaurante. Independientemente de la implementación, la
función recibe un arreglo de asientos que al menos sabemos que tiene una
propiedad `available` que indica si está disponible o no (`{ id: number, available: boolean } `)
y sabiendo esto, retorna la cantidad de asientos disponibles. Esta función, se
podría usar para cualquier establecimiento que tenga una manera de representar
su disponibilidad de asientos en una estructura similar a la que nosotros estamos
esperando.

Ejemplo de un componente:

```jsx
function StudentCard({ student }) {
  return (
    <div>
      <h3>{student.name}</h3>
      <figure>
        <img src={student.photo} alt={student.name} />
      </figure>

      <p>{student.bio}</p>
      <p>
        Contact me @
        <a href={student.github.link} target="_blank">
          <strong>{student.github.username}</strong>
        </a>
      </p>
    </div>
  );
}
```

En este caso, estamos mostrando un componente llamado `StudentCard` que muestra
la información de un estudiante en formato de una tarjeta de presentación. Este
componente recibe un dato de entrada llamado `student` que es un objeto que
contiene propiedades como `name`, `photo`, `bio` y `github`, a cambio nos
devuelve un bloque de una interfaz de usuario que se podría aplicar a cualquier
estudiante que contenga una información similar.

De esta manera, tal vez un poco abstracta aun y sintaxis extraña, pero obviando
estos detalles, podemos ver que un componente es lo más similar a una función
pero aplicada a la interfaz de usuario.

### Ciclo de vida de un componente

Cuando hablamos de la interfaz de usuario en un entorno gráfico, al principio no
nos damos cuenta que esta unidad gráfica pasa por un proceso interno desde que
se muestra hasta que la dejamos de ver. Si nos enfocamos en el navegador, por
ejemplo, nosotros normalmente vemos que ponemos una URL y aparece nuestra página
que hoy en día ya sabemos que está realizada a base de HTML, CSS, JavaScript y
algunos otros recursos estáticos; sin embargo, mucha magia pasa detrás.

Sin ahondar demasiado, el navegador realiza algunos procesos antes de mostrar lo
que terminamos viendo, por ejemplo, el código HTML se convierte a una estructura
de árbol que es lo que conocemos como DOM y CSSOM, en paralelo, está calculando
cuáles de estos elementos realmente se va a mostrar, porque al final puedes tener
una etiqueta HTML con un estilo `display: none;` y este a pesar que existe no se
terminará viendo en el navegador. Una vez calculado estos elementos visibles, se
calcula las posiciones en las que se va mostrar (si bien suena simple, hay mucha
lógica detrás ya que es un espacio 2D pero que se puede agregar complejidad a
través de las diversas propiedades de CSS que existen). Por último, todos estos
cálculos se terminan pintando en el navegador y es lo que terminamos viendo como
interfaz de usuario de un sitio web. Gracias a la capacidad de procesamiento de
las computadoras, no notamos como usuarios todo este proceso y nos da la impresión
de ser mucho más simple de lo que es.

Ok, muy cool to esto como cultura general, pero, ¿en dónde entra el ciclo de
vida en todo esto?. Resulta que hay diversas etapas por la que pasa una interfaz
de usuario:

* Cuando el componente está en proceso de creación, aun no se ve en el entorno,
  pero se está calculando y procesando sus estilos, posición, datos a mostrar,
  etc.
* Cuando se está mostrando, es en este momento en el que decide si debe mostrarse
  en color rojo si algo calculado en el paso previo está mal o en color verde si
  todo está bien, por dar un ejemplo.
* Después de que ya se creó el componente, una vez visible en el entorno.
* Cuando los datos de entrada de un componente se actualiza, si esto ocurre,
  generará casi todo el proceso explicado nuevamente.
* En algunos casos, podemos decidir si a pesar que los datos de entrada cambiaron,
  actualizar el componente o no.
* Después que se hizo una actualización de un componente, esto quiere decir que
  el componente ya existía previamente y esto se puede ejecutar más de una vez.
* Por último, cuando el componente deja de existir, esto sucede normalmente
  cuando lo dejamos de ver, por ejemplo, si estamos viendo un modal, pero al dar
  click en un botón, nos redirecciona a otra página, cuando esto sucede, el modal
  que estábamos viendo, deja de existir.

Este proceso, es algo muy abstracto y que a veces ni pensamos en ellos, pero nos
serán de gran ayuda cuando queramos realizar cierto tipos de acciones que es
mejor manejarlo en cierto momento en vez de otro.

```jsx
class Componente {
  user = null;

  componenteConstruido() {
    // Conéctate a un servicio que te de el usuario
  }

  componenteSeVaActualizar() {
    // Verifica si el usuario está habilitado para ver este componente o no
  }

  componenteActualizado() {
    // Actualiza los estilos del nombre de usuario porque es más largo que X
    // longitud
  }

  componenteDejaraExistir() {
    // Quita todos los eventos que agregaste para que no se queden en memoria
    // innecesariamente
  }

  pinta() {
    // Muestra la interfaz de usuario, ya sea cuando se crea o actualiza
    return (
      <h1>Hola {this.user}</h1>
    );
  }
}
```

El código anterior es un ejemplo en pseudocódigo de cómo se podría ver expresado
el ciclo de vida de un componente.

### Estado de la aplicación

El estado de la aplicación es uno de los conceptos más abstractos que me costó
entender cuando me enfrenté a un framework de front-end moderno, a pesar de que
siempre ha estado presente en todo proyecto que hayamos realizado de manera
intrínseca. La manera en como yo defino este concepto es **todo valor que puede
causar un cambio en la interfaz de usuario**.

¿Qué quiere decir esto? Veamos, todo valor quiere decir que no importa el tipo
que este tenga, puede ser un booleano, número, texto, una estructura más compleja
como un objeto, arreglo, etc. Lo importante viene entender a qué nos referimos
que causa un cambio en la UI; si nos ponemos a analizar más a fondo casos comunes
que encontramos en diversas interfaces, por ejemplo, es común que cuando
mostramos ciertos datos, hagamos un request a un API que nos otorgue dichos datos
y mientras eso ocurre, nosotros agregamos alguna forma de darle al usuario la
experiencia de que estamos procesando su petición pero que toma cierto tiempo.
Para lograr esto, estamos usando un estado, el cual nos indica cuando está
cargando los datos de la API y su valor cambia una vez que obtenemos la respuesta
del servidor. Este cambio de valor en el estado, causa un cambio en la interfaz
de usuario, pues si bien estábamos mostrando un spinner de carga con el valor
inicial del estado, ahora estaríamos mostrando una tabla con el listado de datos.

Imaginemos como se vería en el código:

* Definimos nuestro estado con un valor inicial:

  ```js
  const state = {
    isLoading: true,
  };
  ```

  Aquí estamos indicando que el estado es un objeto (esto porque en una misma
  interfaz puede haber más de un estado que cause cambios de UI) con una
  propiedad indicando que tendremos un estado llamado `isLoading`, el cual es un
  booleano con un valor por defecto de `true`.

* Imaginemos que mandamos a llamar nuestra API de manera asíncrona, y una vez que
  obtengamos la respuesta, actualizaremos el estado:

  ```js
  // En alguna parte de nuestro código
  getStudents({ city: 'Lima' })
    .then((students) => {
      // TODO: Obtenemos las estudiantes y hacemos algo con esos datos

      // Actualizamos el estado de carga, porque ahora ya obtuvimos la respuesta
      component.updateState({ isLoading: false });
    });
  ```

  Aquí estamos mandando a llamar a una función `getStudents()` que recibe ciertos
  argumentos para obtener datos más específicos, dicha función retorna una promesa
  en la cual estamos anidando el método `.then()` para hacer algo cuando la
  promesa se resuelva satisfactoriamente, imaginemos que procesamos los datos
  obtenidos y hacemos algo con ello, pero, además, debemos actualizar la UI para
  que la usuaria no piense que seguimos procesando algo. Los frameworks proveen
  una forma de hacer esta actualización de estado de manera diferente, en este
  ejemplo, estamos usando un método que suponemos viene con cada definición de
  componente.

* Ok, todo bien con actualizar una variable global (eso parece ser lo que hemos
  hecho, sin embargo, la magia viene ahora), pero ahora ¿cómo se actualiza la UI?
  Para esto, debemos de ver donde se define nuestra UI:

  ```js
  <div class="container">
    if (state.isLoading) {
      <Spinner />
    } else {
      <StudentsTable students="?" />
    }
  </div>
  ```

  La sintaxis que estamos usando no es parte de ningún framework en particular,
  lo que busco es dar a mostrar el proceso de cómo el estado afecta nuestra UI.
  Cuando el estado `isLoading` está en `true`, la usuario estará viendo un
  componente `<Spinner />`, y una vez que su valor cambie a `false`, la usuaria
  verá una tabla con las estudiantes que devolvió la API. Solo con un estado,
  pudimos generar un componente con una UI que cambia en base al valor del mismo.

> Nota: En el ejemplo anterior, pudimos haber agregado un par de estados más:
> uno para manejar el error, porque existe la posibilidad que la petición a la
> API falle y en ese caso, no queremos que se siga viendo el spinner de carga ni
> la tabla de estudiantes (o tal vez sí :thinking:, tenemos el poder :muscle:);
> y la colección de estudiantes per se, también es un estado, puesto que si la
> colección cambia porque si la usuaria decide ver las estudiantes de otra ciudad
> a pesar que la tabla que vería sería siendo igual a nivel de presentación, los
> datos representados en dicha tabla cambiarían y eso también es considera un
> cambio de UI.

Con estos conceptos, espero te quede una mejor idea de qué es lo que vas a
comenzar a agregar a tu vocabulario porque probablemente lo vayas a usar/decir
muy a menudo. Ahora si, pasemos a ver la sintaxis de algunos de los frameworks
front-end modernos :rocket:.
