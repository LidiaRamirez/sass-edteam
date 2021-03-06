# SASS EDTEAM

[https://ed.team/cursos/sass](https://ed.team/cursos/sass)

## Compilar sass con Node

~~~
/*  Instalar sass de forma general */
npm install -g node-sass

/* node-sass archivo-entrada archivo-salida */

// Se ejecuta una vez
node-sass styles.scss styles.css

// con --watch, para cada cambio se ejecuta 
node-sass --watch styles.scss styles.css

// para carpetas: node-sass --watch carpeta-entrada --output carpeta-salida
node-sass --watch scss --output css
~~~

## Tabla de contenidos

1. [Sintaxis](#Sintaxis)
    - [Variables](#Variables)
    - [Anidamiento](#Anidamiento)
    - [Tipos de datos](#Tipos-de-datos)
    - [@import](#import)
2. [Ciclos y condicionales](#Ciclos-y-condicionales)
    - [@extend](#extend)
    - [Ciclo @for](#Ciclo-for)
    - [Ciclo @each](#Ciclo-each)
3. [Mixins](#Mixins)
    - Parametros y argumentos
## Sintaxis

### Variables
* Se declaran con `$`
* Tiene un scope si estan dentro de llaves `{ }` solo podrán usarse en esa parte, por ello mayormente se declaran de forma global (fuera de llaves). 
* Se pueden hacer operaciones sin necesidad de colocar la unidad (px, em,rem).

*Ejemplo*
~~~
$size: 200px

body { padding-top: $size + 10; }
~~~

Una opción de las variables es que se pueden sobreescribir usando `!default` tomando el valor que no tiene default.

*Ejemplo*
~~~
$width: 20px !default;

// En otro archivo, ubicado antes o después

$width: 50px;
~~~

Se puede interpolar la variable por ejemplo para crear un selector con nombre de variable. 

*Ejemplo*
~~~
$color: red;

#{color} {
  color: $color;
}

.#{color} {
  color: $color;
}

/* Resultado */

red {
  color: red;
}

.red {
  color: red;
}
~~~

Otro ejemplo de interpolación es para la propiedad css `calc` porque esta propiedad espera dos valores, no espera algo para ser compilado

~~~
$size: 200px;

div {
  width: calc(100% - $size);  
}

section {
  width: calc(100% - #{$size});  
}

/* Resultado */
div {
  width: calc(100% - $size);  
}

section {
  width: calc(100% - 200px);  
}

~~~

### Anidamiento

Permite agregar selectores dentro de otros, una buena practica es tener como máximo 02-03 anidamientos o ninguno para no generar un anidamiento extremo.

*Ejemplo*
~~~
.header {
  ul { padding: 10px; }
  li { display: flex; }
  a { color: blue; }
}
~~~

**Casos de uso**

* Media query.
*Ejemplo*
~~~
.header {
  padding: 20px;
  @media screen and (min-width: 1024px) {
    padding: 10px;
  }
}
~~~

* Animaciones.
*Ejemplo*
~~~
.chat-image {
  animation: rotacion 1s;
  @keyframes rotacion {
    //
  }
}
~~~

* Selector padre con pseudo-elemento, utilizando `&`
*Ejemplo*
~~~
.link {
  &:hover { color: green; }
}
~~~

**Nota**: Una mala practica sería usar con selectores normales
*Ejemplo*
~~~
// mala practica
.link {
  & li { color: green; }
}
~~~

* Clases.
*Ejemplo*
~~~
.menu {
  display: flex;
  &-item { color: blue; }
}

/* Resultado */

.menu {
  display: flex;
  .menu-item { color: blue; }
}
~~~

* Propiedad.
*Ejemplo*
~~~
.main {
  font: {
    family: arial;
    size: 18px;
    weigth: 700;
  }
  grid-template: {
    columns: 100px 100px;
    rows: auto;
  }
}

/* Resultado */

.main {
  font-family: arial;
  font-size: 18px;
  font-weigth: 700;
  grid-template-columns: 100px 100px;
  grid-template-rows: auto;
  }
}
~~~

### Tipos de datos

Es importante porque aveces nos toca validar el tipo de dato pasado ej. en mixis. 

`type-of`: permite saber el tipo de dato

*Ejemplo*
~~~
$var: hola;
type-of($var)
~~~

* String: Se utiliza con comillas o sin comillas

*Ejemplo*
~~~
$var: 'hola';

.header { content: $var; }
~~~

* Números: Son números con unidades (px, em, rem, s)

*Ejemplo*
~~~
$var1: 1em;
$var2: 12px;
$time: 5s;

.animacion { animation-duration: $time; }
~~~

* Colores: 

*Ejemplo*
~~~
$var1: blue;
$var2: darken(red,20);

body {
  background: $var2;
  color: $var1;
}

~~~

* Booleans

*Ejemplo*
~~~
$var: false;
~~~

* List: Son valores de propiedades separadas con espacio o comas
*Ejemplo*
~~~
$var: 1px solid red;
~~~

* Maps: Su sintaxis es parecida a objetos de js
*Ejemplo*
~~~
$colores: (
  primario: red,
  secundario: blue,
  terciario: yellow
);

body {
  color: map-get($colores, secundario);
}
~~~

### @import

Permite estructurar distintos archivos sass y luego unificarlos en uno solo, pero para ello es importante que los archivos secundarios sean archivos parciales `_archivo`
*Ejemplo*
~~~
// Tenemos un archivo parcial _variables.scss y otro archivo styles.scss
@import "variables"

body { background: $color; }
~~~
## Ciclos y condicionales

### @extend

Se utiliza para selectores que comparten estilos en común.

* Con selectores placeholdere
Permite crear selectores placeholder que es como un selector pero inicia con `%` que permite declarar estilos que no se compilan por si solos por ello la directiva `@extend` que permite que un selector placeholder herede los estilos a otro selector. 
*Ejemplo*
~~~
%button {
  display: inline-block;
  margin: 1em 0;
  padding: .5em 1.5em;
}

.button { @extend %button; }
.button-alert {
  @extend %button;
  background: red;
  color: #fff;
}
~~~

* Con clases
*Ejemplo*
~~~
.button {
  display: inline-block;
  margin: 1em 0;
  padding: .5em 1.5em;
}

.button-alert {
  @extend .button;
  background: red;
  color: #fff;
}
~~~
Nota: Es mejor extender un selector de clase sin anidamientos 

*Para pseudoclases
*Ejemplo*
~~~
.button:hover { transform: translateY(-5px); }
.button.active { @extend .button:hover; }

~~~

### Ciclo @for
*Ejemplo1*
~~~
// @for variable from valor-inicio through valor-final
@for $i from 1 through 3 {
  .column-#{i} {
    width: $i*1%;
  }
}

/* Resultado */

.column-1 { width: 1%; }
.column-2 { width: 2%; }
.column-3 { width: 3%; }
~~~

*Ejemplo2*
~~~
@for $i from 1 through 5 {
  @if $i % 2 == 0 {
    .column-#{i} {
       width: $i*5%;
     }
  }  
}

/* Resultado */

.column-2 { width: 10%; }
.column-4 { width: 20%; }
~~~

### Ciclo @each
Sirve para iterar mapas
*Ejemplo*
~~~
$colors: (
  primary: red;
  secondary: green;
  tertiary: blue
)
// @each $key, $value in nombre-mapa 
@each $key, $value in $colors {
  .button-#{$key} { background: $value}
}
~~~

## Mixins
Bloques de código css reutilizables
*Ejemplo*
~~~
@mixin button { 
  display: block; 
  margin: 1em;
}

// se puede incluir en un selector
.button {
  @include button;
}

// Si se declara dentro de mixin una clase no necesita incluir clase
@mixin button { 
  .button {
    display: block; 
    margin: 1em;
  }
}
// un archivo.scss 
@include button;
~~~
### Parámetros y argumentos
*Ejemplo*
~~~
@mixin button($color) {
  .button {
    padding: 14px 20px;
    background-color: $color;
  }
}
// otro archivo
@include button(red)

/* Resultado */

.button {
  padding: 14px 20px;
  background-color: red;
}
~~~

*Ejemplo2*
~~~
    @mixin menu($selector-item, $selector-link) {
      display: flex;
      #{$selector-item} { padding: 5px }
      #{$selector-link} { text-decoration: none; }
    }
  
  // otro archivo 
  .menu { @include menu('.item', '.link') };
  
  /* Resultado */
  .menu { display: flex; }
  .menu .item { padding: 5px; }
  .menu .link { text-decoration: none; }
  
~~~

### Parámetros multiples

*Ejemplo*
~~~
@mixin button($colors...) {
    if length($colors) > 1 {
      @for $i from 2 through length($colors) { 
        &.#{nth($colors,$i) {
          color: nth($colors, $i)
        }
      }
    }
}
~~~

## Funciones 
Sintaxis
~~~
@function nombre-funcion() { 
  //logica de la funcion 
  return $value
}
selector { 
  propiedad: nombre-funcion()
}
~~~

La función solo devuelve un valor 
