За да съвпаднат точно функционалностите на `switch`,`if` трябва да използва стриктно сравнение `'==='`.

За стрингове обаче, просто `'=='` също работи.

```js no-beautify
if(browser == 'Edge') {
  alert("Имаш Edge!");
} else if (browser == 'Chrome'
 || browser == 'Firefox'
 || browser == 'Safari'
 || browser == 'Opera') {
  alert( 'Окей, поддържаме тези браузъри също' );
} else {
  alert( 'Надяваме се тази страница да изглежда ОК!' );
}
```

Забележка: конструкцията `browser == 'Chrome' || browser == 'Firefox' …` е разделена на няколко реда за по-добра четливост.

Но `switch` конструкцията е все още по-чиста и описателна.
