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
input.addEventListener('keydown', () => {
  // TODO: Hacer lo restante una vez lanzado el evento
});
```
