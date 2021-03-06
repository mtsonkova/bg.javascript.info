# Простото, но грешно решение

Простото, но грешно решение би генерирало стойности от `min` до `max` като ги закръгля:

```js run
function randomInteger(min, max) {
  let rand = min + Math.random() * (max - min);
  return Math.round(rand);
}

alert( randomInteger(1, 3) );
```

Функцията работи, но е неправилна. Вероятността да получите гранични стойности `min` и `max` е два пъти по-малко от всеки друг.

Ако изпълнявате примера по-горе много пъти, лесно бихте виждали, това че `2` се появява най-често.

Това се случва, защото `Math.round()` получава произволни числа от интервала `1..3` и ги закръгля по следния начин:

```js no-beautify
стойности от 1    ... до 1.4999999999  стават 1
стойности от 1.5  ... до 2.4999999999  стават 2
стойности от 2.5  ... до 2.9999999999  стават 3
```

Сега можем ясно да видим, това че `1` получава два пъти по-малко стойности от `2`. Същото е с `3`.

# Правилното решение

Има много правилни решения на задачата. Една от тях е да коригира интервалите на границите. За да осигурим същите интервали, можем да генерираме стойности между `0.5 - 3.5`, по този начин добавяме необходимите вероятности към краищата:

```js run
*!*
function randomInteger(min, max) {
  // от (min-0.5) до (max+0.5)
  let rand = min - 0.5 + Math.random() * (max - min + 1);
  return Math.round(rand);
}
*/!*

alert( randomInteger(1, 3) );
```

Алтернативен начин би могъл да бъде използването на `Math.floor` за произволно число от `min` до `max+1`:

```js run
*!*
function randomInteger(min, max) {
  // от min до (max+1)
  let rand = min + Math.random() * (max + 1 - min);
  return Math.floor(rand);
}
*/!*

alert( randomInteger(1, 3) );
```

Сега всички интервали са съчетани по този начин:

```js no-beautify
стойности от 1  ... до 1.9999999999 стават 1
стойности от 2  ... до 2.9999999999 стават 2
стойности от 3  ... до 3.9999999999 стават 3
```

Всички интервали имат една и съща дължина, което прави крайното разпределение равномерно.
