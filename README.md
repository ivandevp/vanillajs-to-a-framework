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
