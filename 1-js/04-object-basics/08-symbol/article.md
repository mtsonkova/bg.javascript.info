
# Тъпът *Symbol* - Символ

По спецификация ключовете за свойствата на обекта могат да бъдат или от тип низ, или от тип символ. Не са числа или булеви, а низове или символи, само тези два типа.

Досега използвахме само низове. Сега нека да видим предимствата, които символите могат да ни дадат.

## *Symbol*

*Symbol* представлява уникален идентификатор.

Стойност от този тип може да бъде създадена с `Symbol()`:

```js
// id е нов Символ
let id = Symbol();
```

При създаването на съмволи, ние можем да им подадем описание (наричано също като име на символ), най-вече полезни при отстраняване на грешки:

```js
// id е символ с описание "id"
let id = Symbol("id");
```

Символите са гарантирани, че ще са уникални. Дори да създадем много символи с едно и също описание, те ще имат различни стойности. Описанието е просто етикет, който не влияе на нищо.

Например, тук има два символа със същото описание -- те не са равни:

```js run
let id1 = Symbol("id");
let id2 = Symbol("id");

*!*
alert(id1 == id2); // false
*/!*
```

Ако сте запознати с Ruby или друг език, който също има някакви „символи“ - моля, не се заблуждавайте. Символите при JavaScript са различни.

````warn header="Symbols don't auto-convert to a string"
Повечето стойнисти в JavaScript поддържат имплицитно преобразуване към низ.
Например, можем да използваме фунцкията `alert` почти с всяка стойност, и то ще работи.
Символите са специални. Те не се преобразуват автоматично.

Например, този `alert` ще ни покаже грешка:

```js run
let id = Symbol("id");
*!*
alert(id); // TypeError: Cannot convert a Symbol value to a string
*/!*
```

Това е „езикова защита“ срещу объркване, защото низовете и символите са коренно различни и не бива случайно да ги превръщаме в други.

Ако наистина искаме да покажем символа, трябва експлицитно да извикаме `.toString()` метода, като тук:
```js run
let id = Symbol("id");
*!*
alert(id.toString()); // Symbol(id), сега то работи
*/!*
```

Или `symbol.description` свойството, за да покажем само описанието му:
```js run
let id = Symbol("id");
*!*
alert(id.description); // id
*/!*
```

````

## "Скрити" свойства

Символите ни позволяват да създадем „скрити“ свойства на обект, до които никоя друга част от кода не може случайно да получи достъп или да презапише.

Например, ако работим с `user` обекти, които принадлежат на чужд код. Бихме искали да добавим идентификатори към тях.

Нека да използваме символен ключ за тях:

```js run
let user = { // принадлежи към чужд код
  name: "John"
};

let id = Symbol("id");

user[id] = 1;

alert( user[id] ); // можем да получим достъп до данните, използвайки символа като ключ
```

Каква е ползата от използването `Symbol("id")` над низ `"id"`?

Защото `user` обектите принадлежат на чужд код, и този код също се използва от тях и ние не трябва да добавяме допълнителни полета към него. Това е опасно. Но до символа не може да се достъпи случайно и чуждият код вероятно дори няма да го види, така че вероятно всичко това е правилно да се направи.

Освен това, представете си, че друг скрипт иска да има свой идентификатор вътре в `user` обекта, за собствени цели. Това може да е друга библиотека на JavaScript, така че скриптовете не са напълно наясно един с друг.

Тогава този скрипт може да създаде свой собствен `Symbol("id")`, като тук:

```js
// ...
let id = Symbol("id");

user[id] = "Тяхното id";
```

Няма да има конфликт между наши и техни идентификатори, защото символите винаги са различни, дори и да имат същото име.

...Но ако бяхме използвали низ `"id"` вместо символ със същата цел, тогава би било *възможно* да имаме конфликт:

```js run
let user = { name: "John" };

// Нашият скрипт използва свойството "id"
user.id = "Нашето id";

// ...Друг скрипт също иска "id" за целите си...

user.id = "Тяхното id"
// Бум! презаписан от друг скрипт!
```

### Символите в литерали

Ако искаме да използваме символ с литерал на обект `{...}`, имаме нужда от квадратни скоби около него.

Като тук:

```js
let id = Symbol("id");

let user = {
  name: "John",
*!*
  [id]: 123 // не "id: 123"
*/!*
};
```
Това е така, защото се нуждаем от стойността от променливата `id` като ключ, не като низ "id".

### Символите се пропускат от for..in

Символните свойства не участват в `for..in` цикли.

Например:

```js run
let id = Symbol("id");
let user = {
  name: "John",
  age: 30,
  [id]: 123
};

*!*
for (let key in user) alert(key); // name, age (няма символи)
*/!*

// но директният достъп от символа работи
alert( "Direct: " + user[id] );
```

`Object.keys(user)` също ги игнорира. Това е част от общия принцип "скриване на символни свойства". Ако друг скрипт или библиотека се изцикли над нашия обект, той няма неочакван достъп до символните свойства.

За разлика, [Object.assign](mdn:js/Object/assign) копира и символните и низовите свойства:

```js run
let id = Symbol("id");
let user = {
  [id]: 123
};

let clone = Object.assign({}, user);

alert( clone[id] ); // 123
```

Тук няма парадокс. Това е по дизайн. Идеята е, че когато клонираме обект или обединяваме обекти, обикновено искаме *всички* свойства да бъдат копирани (включително символи като `id`).

## Глобални символи

Както видяхме, обикновено всички символи са различни, дори и да имат същото име. Но понякога искаме едноименните символи да са едни и същи единици. Например, различните части от приложението ни искат достъп до символ `"id"` което означава точно същото свойство.

За да го постигнем това, съществува *глобален регистър на символите*. Можем да създадем символи в него и да получим достъп до тях по-късно, и то гарантира, че многократните достъпи с едно и също име връщат точно същия символ.

За да можем да прочетем (или да създадем ако отсъства) символ от регистъра, използваме `Symbol.for(key)`.

Така проверяваме глобалния регистър, и ако има символ, описан като `key`, след това го връща, в противен случай създава нов символ `Symbol(key)` и го съхранява в регистъра от дадения `key`.

Например:

```js run
// четем от глобалния регистър
let id = Symbol.for("id"); // ако символът не е съществува, той бива създаден

// прочетете го отново (може би от друга част на кода)
let idAgain = Symbol.for("id");

// един и същ символ
alert( id === idAgain ); // true
```

Символите вътре в регистъра се наричат *глобални символи*. Ако искаме символ за приложението ни, достъпен навсякъде в кода -- за са точно затова.

```smart header="That sounds like Ruby"
В някои езици за програмиране, като Ruby, има само един символ за име.

В JavaScript, както виждаме, това е точно за глобалните символи.
```

### Symbol.keyFor

За глобални символи, не само `Symbol.for(key)` връща символ от име, но има обратното: `Symbol.keyFor(sym)`,то  връща име от глобален символ.

Например:

```js run
// символ от име
let sym = Symbol.for("name");
let sym2 = Symbol.for("id");

// име от символ
alert( Symbol.keyFor(sym) ); // name
alert( Symbol.keyFor(sym2) ); // id
```

`Symbol.keyFor` вътрешно използва глобалния регистър на символите, за да потърси ключа за символа. Така че то не работи за не глобални символи. Ако символът не е глобален, няма да може да го намери и се ще върне `undefined`.

Така казано, всякакви символи имат `description` свойство.

Например:

```js run
let globalSymbol = Symbol.for("name");
let localSymbol = Symbol("name");

alert( Symbol.keyFor(globalSymbol) ); // name, глобален символ
alert( Symbol.keyFor(localSymbol) ); // undefined, не глобален символ

alert( localSymbol.description ); // name
```

## Системни символи

Има много "системни" символи, което JavaScript използва вътрешно, и можем да ги използваме за финна настройка на различни аспекти на нашите обекти.

Те са изброени в таблицата със спецификациите в [Well-known symbols](https://tc39.github.io/ecma262/#sec-well-known-symbols):

- `Symbol.hasInstance`
- `Symbol.isConcatSpreadable`
- `Symbol.iterator`
- `Symbol.toPrimitive`
- ...и т.н.

Например, `Symbol.toPrimitive` ни позволява да опишем обект до примитивно преобразуване. Ще видим как точно става това съвсем скоро..

Други символи също ще станат познати, когато изучим съответните езикови характеристики.

## Обобщение

`Symbol` е примитивен тип за уникални идентификатори.

Символите се създават с `Symbol()` с описание по избор (име).

Символите са винаги различни стойности, дори и да имат същото име. Ако искаме едноименните символи да са равни, тогава трябва да използваме глобалния регистър: `Symbol.for(key)` връща (създава ако е нужно) глобален символ с ключ `key` като име. Множество извиквания на `Symbol.for` със същия ключ `key` връща точно същия символ.

Символите имат два основни случая на употреба:

1. "Скрити" свойства на обект.
    Ако искаме да добавим свойство в обект, който "принадлежи" на друг скрипт или библиотека, можем да създадем символ и да го използваме като ключ на свойството. Символините свойства не се показват в `for..in`, така че няма да бъдат обработени случайно заедно с другите свойства. Също така няма да има достъп до него директно, защото друг скрипт няма нашия символ. Така че свойството ще е защитено от случайна употреба или презаписване.

    Значи ние можем "тайно" да скрием нещо в обектите, което ни трябват, но на други не би трябвало да го видят, използвайки символичните свойства.

2. Има много системни символи, използвани от JavaScript, които са достъпни като `Symbol.*`. Можем да ги използваме, за да променим някои вградени поведения. Например, по-късно в ръководството, ще използваме `Symbol.iterator` за [iterables](info:iterable), `Symbol.toPrimitive` за да настроим [обръщане на обект към примитивна стойност](info:object-toprimitive) и т.н. .

Технически, символите не са 100% скрити. Има вграден метод [Object.getOwnPropertySymbols(obj)](mdn:js/Object/getOwnPropertySymbols), което ни позволява да вземем всички символи. Също така има и метод наречен [Reflect.ownKeys(obj)](mdn:js/Reflect/ownKeys), което връща *всички* ключове на обект включително и символните. Така че те всъщност не са наистина скрити. Но повечето библеотеки, вградените функции и синтаксисните конструкции не използват тези методи.