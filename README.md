# Polyfill for CSS `attr()` Function
*An idea to use **HTML Custom Data Attributes** and **CSS Custom Properties** as a polyfill for the (currently-limited) CSS `attr()` function*

## HTML
```
<div class="square"></div>
<div class="square" data-width="50px" data-height="50px" data-background-color="orange"></div>
<div class="square" data-height="150px" data-background-color="yellow" data-border-radius="50% 50% 0 0"></div>
<div class="square" data-background-color="green" data-border-radius="50%"></div>
```

## CSS

```
.square {
  display: inline-block;
  margin-right: 12px;
}
```

```
/* This doesn't currently work */

.square {
  width: 100px;
  height: 100px;
  background-color: red;
  border-radius: 0;
}

.square {
  width: attr(data-width);
  height: attr(data-height);
  background-color: attr(data-background-color);
  border-radius: attr(data-border-radius);
}
```

```
/* But this, combined with the JavaScript below, does work */

.square {
  width: var(--width, 100px);
  height: var(--height, 100px);
  background-color: var(--background-color, red);
  border-radius: var(--border-radius, 0);
}
```

## JavaScript

```
const squares = document.querySelectorAll('.square');
const toProperty = (key) => key.replace(/([a-z])([A-Z])/g, '$1-$2').toLowerCase();
squares.forEach((square) => {Object.keys(square.dataset).forEach((key) => square.style.setProperty(`--${toProperty(key)}`, square.dataset[key]))});
```

_______

## Questions and Answers

*This is a bit silly, isn't it?*
```
<div class="square" data-width="50px" data-height="50px" data-background-color="orange"></div>
```
*plus the CSS, plus the JS, is... just the same as:*
```
<div class="square" style="width:50px; height:50px; background-color:orange;"></div>
```
*isn't it?*

Mostly, yes, but 

 - `document.querySelector('.square').getAttribute('style')`

isn't an object, whereas

 - `document.querySelector('.square').dataset`

is.

*Right. But then:*

```
let myObject = {};
let myArray = document.querySelector('.square').getAttribute('style');
myArray = myArray.split(';').map((element) => element.trim()).filter((element) => element.length > 0);
myArray.forEach((element) => myObject[element.split(':')[0]] = element.split(':')[1]);
```

*gives you that same object (more or less), doesn't it?*
