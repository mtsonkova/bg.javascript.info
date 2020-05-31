Има много алгоритми за тази задача.

Нека да използваме вложени цикъли:

```js
For всеки i in вИнтервала {
  провери if i дали има делител от 1..i
  if да => стойността не е проста
  if no => стойността е проста, покажи я
}
```

Кодът използващ на етикет:

```js run
let n = 10;

nextPrime:
for (let i = 2; i <= n; i++) { // за всяко i...

  for (let j = 2; j < i; j++) { // потърсете делител..
    if (i % j == 0) continue nextPrime; // не е просто, продължете със следващия i
  }

  alert( i ); // е просто
}
```

Има много място за оптимизирането му. Например, бихме могли да потърсим делителите от `2` до квадратен корен на `i`. Но така или иначе, ако искаме да бъдем наистина ефективни за големи интервали, трябва да променим подхода и да разчитаме на усъвършенствани математически и сложни алгоритми като [Решето на Ератостен](https://bg.wikipedia.org/wiki/%D0%A0%D0%B5%D1%88%D0%B5%D1%82%D0%BE_%D0%BD%D0%B0_%D0%95%D1%80%D0%B0%D1%82%D0%BE%D1%81%D1%82%D0%B5%D0%BD) и други.