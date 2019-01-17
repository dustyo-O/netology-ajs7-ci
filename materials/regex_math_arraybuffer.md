---
title: RegEx, Math, ArrayBuffer - Презентация
slideOptions:
  transition: slide
---

# AJS. RegEx, Math, ArrayBuffer

###### tags: `netology` `advanced js`

---

# RegExp

![](https://i.imgur.com/ggiy7is.png)

---

При поиске "regexp" Яндекс намекает на какие-то **регулярные выражения**:

![](https://i.imgur.com/Sl7Oxyx.png)

---

## Что такое "регулярные выражения"?

[Википедия](https://ru.wikipedia.org/wiki/Регулярные_выражения):
**Регулярные выражения** - формальный язык поиска и осуществления манипуляций с подстроками в тексте, основанный на использовании метасимволов.

*Это общее название технологии для манипуляций с подстроками.*

---

[MDN Web Docs](https://developer.mozilla.org/ru/docs/Web/JavaScript/Guide/Regular_Expressions):
**Регулярные выражения** - это шаблоны используемые для сопоставления последовательностей символов в строках.

*А также частное название самих выражений, на которых основана технология.*

---

Ну, сопоставление последовательности символов мы выполнять умеем и без этих регулярных выражений.

Например, нам надо произвести валидацию адреса электронной почты: проверить, что адрес электронной почты содержит знак `@`.

Каким способом можно это сделать?

---

Можно перебрать каждый символ (самое неправильное решение):
```javascript=
function validateEmail(emailStr) {
  for (const itemSymbol of emailStr) {
    if (itemSymbol === '@') {
      return true;
    }
  }
  return false;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('supportnetology.ru'));
```

---

Можно найти позицию вхождения подстроки (уже решение получше):

```javascript=
function validateEmail(emailStr) {
  return emailStr.indexOf("@") !== -1;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('supportnetology.ru'));

```

---

Можно же найти по шаблону-регулярному выражению (пока непонятно, лучше ли):

```javascript=
function validateEmail(emailStr) {
  return emailStr.search(/@/) !== -1;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('supportnetology.ru'));

```

---

В реальных задачах редко требуется найти совпадение лишь по одному символу, зачастую требуется, чтобы строка  нескольким условиям. 

Напаример, чтобы понять, насколько жизнеспособно каждое из наших решений, давайте введем еще одно правило - в адресе электронной почты после собаки должна присутствовать точка.

---

Первое выражение примет прямо-таки страшный вид:
```javascript=
function validateEmail(emailStr) {
  let foundComercialAt = false;
  for (const itemSymbol of emailStr) {
    if (itemSymbol === '@') {
      foundComercialAt = true;
    }
    if ((itemSymbol === '.') && (foundComercialAt)) {
      return true;
    }
  }
  return false;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('supportnetology.ru'));
```

---

Второе решение попроще:

```javascript=
function validateEmail(emailStr) {
  const commercialAtPos = emailStr.indexOf("@");
  return (commercialAtPos !== -1) && (commercialAtPos < emailStr.indexOf("."));
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('supportnetology.ru'));

```

---

Решение с регулярным выражением же немного удивляет:

```javascript=
function validateEmail(emailStr) {
  return emailStr.search(/@\w+\./) !== -1;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('supportnetology.ru'));
```

---

Если же еще ввести требования, что собака не является первым символом адреса, а точка не является последней..

Первый и второй способ станут еще сложнее.

---

> без демонстрации в консоли

Первый способ (не делайте такого, пожалуйста, в своей работе):
```javascript=
function validateEmail(emailStr) {
  let foundComercialAt = false;
  const cuttedEmailStr = emailStr.substring(1, emailStr.length - 1);
  for (const itemSymbol of cuttedEmailStr) {
    if (itemSymbol === '@') {
      foundComercialAt = true;
    }
    if ((itemSymbol === '.') && (foundComercialAt)) {
      return true;
    }
  }
  return false;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

---

Второй способ:
```javascript=
function validateEmail(emailStr) {
  const commercialAtPos = emailStr.indexOf("@");
  const dotPos = emailStr.indexOf(".");
  if (commercialAtPos <= 0) {
    return false;
  }
  if (dotPos < commercialAtPos) {
    return false;
  }
  if (dotPos === emailStr.length - 1) {
    return false;
  }
  return true;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

---

Третий же притерпит всего пару изменений:
```javascript=
function validateEmail(emailStr) {
  return emailStr.search(/\w@\w+\.\w/) !== -1;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

---

**Регулярное выражение - это шаблон подстроки.**

Например, строка `\w@\w+\.\w` читается как:
1. `\w` - любая цифра, буква или знак подчеркивания
2. `@` - символ @
3. `\w+` - любая цифра, буква или знак подчеркивания, один символ или более
4. `\.` - точка

И функция `search()` производит поиск последовательности, подходящей под шаблон в строке `emailStr`.

---

С помощью сайта [https://regex101.com](https://regex101.com) можно посмотреть совпадение подстроки с шаблоном.

![](https://i.imgur.com/joReIKU.png)

---

Синтаксис регулярок (регулярных выражений) достаточно прост.

`/регулярное выражение/` записывается между символами "/"

например: `/my_regexp/`

регулярное выражение записывается с помощью специального языка.
Например:
`\d` - десятичная цифра
`\D` - любой символ, кроме десятичной цифры

Многие символы в регулярках можно писать явно:
`a` - символ "a"
`Я` - буква "Я"

---

У регулярных выражений достаточно большое количество различных возможностей, все их осветить в рамках данной лекции не удастся, однако изучить их стоит. 

Сам язык регулярных выражений во многих языках программирования единый, поэтому освоить его стоит в любом случае.

В конце презентации есть шпаргалка по возможностям регулярок.

В ходе занятия же мы рассмотрим некоторые из них при знакомстве с функциями.

---

## str.search(RegExp)
Этот метод возвращает позицию первого совпадения с шаблоном регулярного выражения.
Если совпадения нет, то результатом выполнения будет `-1`

Синтаксис:
```javascript
str.search(RegExp);
```
Один пример использования рассмотрен выше.

---

```javascript=
function validateEmail(emailStr) {
  return emailStr.search(/\w@\w+\.\w/) !== -1;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

В этом примере производится поиск совпадения с шаблоном. Если поиск удачен, то возвращается число большее или равное нулю, на что и производится проверка в условии.

---

## str.match(RegExp)

Возвращает результат совпадения с шаблоном.

Попробуем записать предыдущую проверку через `match()`.
Для наглядности пока просто выведем результат выполнения `match()`

```javascript=
function validateEmail(emailStr) {
  return emailStr.match(/\w@\w+\.\w/);
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

---

Если `search()` оповещает только о факте совпадения, то `match()` нам показывает и сам результат сверки с шаблоном.

![](https://i.imgur.com/0uAMXza.png)

Т.е., если совпадений не найдено, то результатом `match()` будет `null`

---

Проверка приобретает такой вид:

```javascript=
function validateEmail(emailStr) {
  return emailStr.match(/\w@\w+\.\w/) !== null;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

---

Давайте изменим нашу регулярку:
```javascript
/^\w+@\w+\.\w+$/
```

`^` Обозначает начало строки (не внутри скобок)
`$` Обозначает конец строки

Это позволит нам валидировать строку полностью.

---

```javascript=
function validateEmail(emailStr) {
  return emailStr.match(/^\w+@\w+\.\w+$/) !== null;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('supp@rt@netologyru'));
console.log(validateEmail('$upport@netologyru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```
---

## str.split(RegExp|str, limit)

Функция `split()` позволяет разбить строку по какому-либо разделителю.

Как разделитель можно использовать как строку, так и регулярку.

`limit` - необходимое количество полученных элементов массива, по умолчанию - неограничено.

---

Разобьем предложение на слова:

```javascript=
function separatePhrase(phrase) {
  return phrase.split(/[^(а-яёa-z@\.)]+/i);
}
console.log(separatePhrase('Support@netology.ru - адрес технической поддержки Нетологии.'));
```

`^` - внутри скобок используется для отрицания
`[]` - группа возможных символов
`а-я` - указывает на диапазон символов (от "а" до "я")
Флаг `i` указывает на игнорирование регистра.

---

## str.replace(RegExp, str|func)

Чудо, а не функция.
Позволяет произвести определенные операции с определенными участками текста.

---

Давайте напишем функцию перевода на "кирпичный язык".


Кто помнит, как перевести на "кирпичный"? Можно просто пример.

> После каждой гласной добавляется слог с буквой "к" и с той же гласной.

---

В круглых скобках указывается группа сиволов.

Для получения выбранной группы используем `$1`

```javascript=
function transferToBrick(phrase) {
  return phrase.toUpperCase().replace(/([АЯЭЕОЁУЮЫИ])/, '$1K$1');
}
console.log(transferToBrick('Привет, мир!'));
```

---

Флаг `g` указывает на то, что поиск будет производиться по всей фразе. Без этого флага будет изменен только первый символ. 


```javascript=
function transferToBrick(phrase) {
  return phrase.toUpperCase().replace(/([АЯЭЕОЁУЮЫИ])/g, '$1K$1');
}
console.log(transferToBrick('Привет, мир!'));
```

---

## RegExp.test(str)

Функция, cхожая с `str.search(regexp) !== -1`. Проверяет, есть ли хоть одно совпадение.

```javascript=
function validateEmail(emailStr) {
  return /\w@\w+\.\w/.test(emailStr);
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

---

## RegExp.exec(str)

Функция, похожая на `str.match(RegExp)`

```javascript=
function validateEmail(emailStr) {
  return /^\w+@\w+\.\w+$/.exec(emailStr) !== null;
}

console.log(validateEmail('support@netology.ru'));
console.log(validateEmail('supp@rt@netologyru'));
console.log(validateEmail('$upport@netologyru'));
console.log(validateEmail('support@netologyru'));
console.log(validateEmail('@supportnetology.ru'));
console.log(validateEmail('support@netologyru.'));
console.log(validateEmail('supportnetology.ru'));
console.log(validateEmail('supportnetologyru'));
```

---

Кстати, если нам потребуется вычленить адрес электронной почты из предложения:

```javascript=
function findEmail(emailStr) {
  return /\w+@\w+\.\w+/.exec(emailStr);
}

console.log(findEmail('Support@netology.ru - адрес технической поддержки Нетологии.')[0]);
```

Аналогичный результат принесёт и функция `str.match(RegExp)`

---

## RegExp.toString()

Возвращает строковое представление регулярного выражения.

```javascript
const myReg = /^\w+@\w+\.\w+$/g;
console.log(typeof(myReg));
console.log(typeof(myReg.toString()));
// -> object
// -> string
```

---

## Возможности регулярных выражений в стандарте ES2018

* именованные группы 
* +-lookbehind, +-lookahead
* флаг `s`
* паттерны для работы с Unicode

Эти возможности установлены стандартом ES2018, но ещё (на момент написания лекции) реализованы не во всех браузерах.

---

**Именованные группы**
В "js будущего" появилась возможность давать группам наименования.

```javascript=
function findEmail(emailStr) {
  return /(?<emailGroup>\w+@\w+\.\w+)/.exec(emailStr);
}

const textStr = 'Support@netology.ru - адрес технической поддержки Нетологии.';
const emailStr = findEmail(textStr);
console.log(emailStr.groups.emailGroup);
```

---

**lookbehind, lookahead**
"Взгляд назад" и "Взгляд вперёд": 
* x(?=y) - ищет соответствие паттерну x, когда он идёт перед y  (положительная опережающая проверка)
* x(?!y) - ищет соответствие паттерну x, когда он идёт не перед y (негативная опережающая проверка)
* (?<=y)x - ищет соответствие паттерну х, когда он идёт после y (положительная ретроспективная проверка)
* (?<!y)x - ищет соответствие паттерну х, когда он идёт не после y (негативная ретроспективная проверка)

```javascript=
function findEmail(emailStr) {
  return emailStr.match(/\w+(?=@)/g);
}

textStr = 'admin@netology, email: support@netology.ru';
console.log(findEmail(textStr));

// -> ["admin", "support"] 
```

---

**Флаг 's' (dotAll)**

Хотя и считается, что символ точки соответствует любому одиночному символу, он не соответствует некоторым символам, например, символу перевода строки `\n`.

```javascript=
function matchPhrase(phraseStr) {
  return /Нетология.онлайн-школа/.exec(phraseStr);
}

const textStr = 'Нетология\nонлайн-школа';
console.log(matchPhrase(textStr));

// -> null
```

Флаг `s` позволяет видеть как точку абсолютно любой символ:

```javascript=
function matchPhrase(phraseStr) {
  return /Нетология.онлайн-школа/s.exec(phraseStr);
}

const textStr = 'Нетология\nонлайн-школа';
console.log(matchPhrase(textStr));

// -> ["Нетология↵онлайн-школа", index: 0, input: "Нетология↵онлайн-школа", groups: undefined]
```

---

**Паттерны для работы с Unicode-символами**

Были расширены возможности для работы с Unicode:

```javascript
console.log(/\p{Emoji}/u.test('😀ΩU'));
console.log(/\p{Script=Greek}/u.exec('😀ΩU'));

// -> true
// -> ["Ω", index: 0, input: "😀ΩU", groups: undefined]
```

---

## РЕГУЛЯРНОЕ ВЫРАЖЕНИЕ - ЗЛО, если...

Регулярные выражения - удобный, полезный, высокопроизводительный и простой инструмент.
Однако, важно помнить, что:
* как и любой другой инструмент, он НЕ универсален
* регулярное выражение сложно читаемо

Достаточно часто разработчики могут создать "дьявольское" регулярное выражение. 
Так же, среди разработчиков бытует мнение, что "регулярки пишутся в одну сторону" - то есть их пишут, но не читают, регулярное выражение проще написать с нуля, чем разобраться в готовом.

---

Поэтому, 
**РЕГУЛЯРНОЕ ВЫРАЖЕНИЕ - ЗЛО, если:**
**1. Есть более подходящие инструменты.** 

Например, для проверки кода есть большое количество готовых синтаксических анализаторов. Неправильно проверять "правильно ли сформирован JSON" собственной регуляркой.

Например, если требуется только определить наличие единственного символа в строке, с этим справится и функция `str.indexOf()`.

Например, Вам требуется найти наличие двух подстрок в тексте. Может быть, код для поиска двух подстрок будет эффективнее, чем одна регулярка?

Например, не пытайтесь проверить текст на цензурность с помощью одной регулярки. Эта проблема эффективнее решается несколькими различными инструментами, где регулярным выражениям отведена вспомогательная роль.

---

**РЕГУЛЯРНОЕ ВЫРАЖЕНИЕ - ЗЛО, если:**
**2. Вы пытаетесь решить одной регуляркой задачу, которую НУЖНО решать несколькими регулярными выражениями**

Например, Вам требуется проверить документ. Если у вас допускается два документа (например, паспорт и свидетельство о рождении), не стоит пытаться объединить два шаблона для проверки в один. Даже если Вы получите выигрыш в производительности (что маловероятно), Вы существенно потеряете в читаемости кода.

---

**РЕГУЛЯРНОЕ ВЫРАЖЕНИЕ - ЗЛО, если:**
**3. Регулярное выражение нечитаемо.**

Например, с вероятностью 99,9% другой разработчик не станет разбираться в смысле следующей регулярки:

```
(([0-9a-fA-F]{1,4}:){7,7}[0-9a-fA-F]{1,4}
|([0-9a-fA-F]{1,4}:){1,7}:|([0-9a-fA-F]{1,4}:)
{1,6}:[0-9a-fA-F]{1,4}|([0-9a-fA-F]{1,4}:){1,5}
(:[0-9a-fA-F]{1,4}){1,2}|([0-9a-fA-F]{1,4}:){1,4}
(:[0-9a-fA-F]{1,4}){1,3}|([0-9a-fA-F]{1,4}:){1,3}
(:[0-9a-fA-F]{1,4}){1,4}|([0-9a-fA-F]{1,4}:){1,2}
(:[0-9a-fA-F]{1,4}){1,5}|[0-9a-fA-F]{1,4}:
((:[0-9a-fA-F]{1,4}){1,6})|:((:[0-9a-fA-F]{1,4})
{1,7}|:)|fe80:(:[0-9a-fA-F]{0,4}){0,4}
%[0-9a-zA-Z]{1,}|::(ffff(:0{1,4}){0,1}:){0,1}
((25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])\.){3,3}
(25[0-5]|(2[0-4]|1{0,1}[0-9]){0,1}[0-9])|
([0-9a-fA-F]{1,4}:){1,4}:((25[0-5]|(2[0-4]|1{0,1}
[0-9]){0,1}[0-9])\.){3,3}(25[0-5]|(2[0-4]|1{0,1}
[0-9]){0,1}[0-9]))
```

---

**РЕГУЛЯРНОЕ ВЫРАЖЕНИЕ - ЗЛО, если:**
**4. Вы не умеете писать регулярные выражения или Ваш код "грязный".**

Если регулярное выражение не работает - оно вредит.
Если регулярное выражение слишком требовательное к ресурсам - оно вредит.
Если в мешанине кода всплывает регулярное выражение - оно вредит.

---

# Math
![](https://i.imgur.com/a1FRBjO.jpg)

---

Объект `Math` содержит набор математических функций и констант.

---

**Свойства (математические константы):**

`Math.E` - число Эйлера, так же известное, как математическая константа, обозначаемая символом *e*.

`Math.PI` - отношение длины окружности её диаметру, так же известно как "число пи".

`Math.LN2` - натуральный логарифм из 2.
`Math.LN10` - натуральный логарифм из 10.
`Math.LOG2E` - двоичный логарифм из числа Эйлера.
`Math.LOG10E` - десятичный логарифм из E.

`Math.SQRT1_2` - квадратный корень из 1/2.
`Math.SQRT2` - квадратный корень из 2.

---

**Округление:**

`Math.ceil(x)` - возвращает наименьшее целое число, большее, либо равное указанному числу ("округление вверх").
`Math.floor(x)` - возвращает наибольшее целое число, меньшее, либо равное указанному числу ("округление вниз").
`Math.round(x)` - возвращает значение числа, округлённое до ближайшего целого.
`Math.trunc(x)` - возвращает целую часть числа, убирая дробные цифры.

---

**Тригонометрические функции:**

`Math.acos(x)` - возвращает арккосинус числа.
`Math.asin(x)` - возвращает арксинус числа.
`Math.atan(x)` - возвращает арктангенс числа.
`Math.cos(x)` - возвращает косинус числа.
`Math.sin(x)` - возвращает синус числа.
`Math.tan(x)` - возвращает тангенс числа.

*Внимание! Тригонометрические функции  принимают в параметрах или возвращают углы в радианах.*

---

**Прочие функции:**

`Math.abs(x)` - возвращает абсолютное значение (модуль) числа.
`Math.sqrt(x)` - возвращает положительный квадратный корень числа.
`Math.max([x[, y[, …]]])` - возвращает наибольшее число из своих аргументов.
`Math.min([x[, y[, …]]])` - возвращает наименьшее число из своих аргументов.
`Math.random()` - возвращает псевдослучайное число в диапазоне от 0 до 1.
`Math.log(x)` - возвращает натуральный логарифм числа.
`Math.cbrt(x)` - возвращает кубический корень числа.

---

Это далеко не полный перечень.
Современный объект `Math` дает полноценный математический инструментарий.

---

Чтобы использовать этот инструментарий, требуется обратиться к объекту `Math`

```javascript=
const randomDigit = Math.random();
console.log(randomDigit);

const xGrad = Math.round(randomDigit * 180);
console.log(xGrad);

const xRad = xGrad * Math.PI / 180;
console.log(xRad);

const cosX = Math.cos(xRad);
const sinX = Math.sin(xRad);

console.log(cosX);
console.log(sinX);
console.log(cosX**2 + sinX**2);
```

---

Как найти длину гипотенузы при известных катетах через JS?

Напомню, квадрат гипотенузы равен сумме квадратов катетов

```javascript=
const cathetusFirst = 3;
const cathetusSecond = 4;
const hypotenuse = .....;

console.log(hypotenuse);
```

---

```javascript=
const cathetusFirst = 3;
const cathetusSecond = 4;
const hypotenuse = Math.hypot(cathetusFirst, cathetusSecond);

console.log(hypotenuse);
```

---

# ArrayBuffer
![](https://i.imgur.com/paAnJxg.jpg)

---

Автоматическое управление памятью упрощает разработку приложения, однако снижает производительность программ.

Например, когда вы создаёте переменную, js-движок определяет, какого типа будет эта переменная, какой объем памяти ей потребуется. 

Достаточно часто это ведет к резервированию большего объема памяти, чем на самом деле нужно для хранения переменной. При этом требуемый размер может быть в 2-8 раз меньше, чем резервируемый. Это приводит к неэффективному использованию памяти.

---

Во многих случаях, автоматическое управление памятью не вызывает проблем. Большинство JS-приложений не настолько требовательны к производительности, чтоб им было необходимо ручное управление памятью. Однако, ручное управление памятью негативно влияет на производительность труда программиста.

Случаи, в которых требуется ручное управление памятью - это случаи требовательных к оперативной памяти js-приложений.

---

Как одно из решений для ручного контроля управления памятью можно рассмотреть `ArrayBuffer`.

---

## ArrayBuffer

ArrayBuffer представляет собой ссылку на поток "сырых" данных.

Например, если создать `ArrayBuffer` длины 4:

```javascript=
const buffer = new ArrayBuffer(4);
```

Мы получим просто данные следующего вида (в байтах): `0000`

---

Эти данные можно представить в разных вариантах: разделив по байту, 2 байта, или четыре.

Чтобы представить `ArrayBuffer` в каком-либо виде, требуется создать его представление:


```javascript
const buffer = new ArrayBuffer(4);
const buffer8BitView = new Int8Array(buffer); 
    
console.log(buffer8BitView);

// -> Int8Array(4)[0, 0, 0, 0];
```

---

Этому же буферу можно азадть и другое представление:


```javascript
const buffer = new ArrayBuffer(4);
const buffer8BitView = new Int8Array(buffer); 
const buffer16BitView = new Int16Array(buffer); 
    
console.log(buffer8BitView);
console.log(buffer16BitView);

// -> Int8Array(4)[0, 0, 0, 0];
// -> Int16Array(2)[0, 0];
```

---

После создания представления можно в `ArrayBuffer` внести данные или изъять из него:

```javascript=
const buffer = new ArrayBuffer(4);
const buffer8BitView = new Int8Array(buffer); 
const buffer16BitView = new Int16Array(buffer); 

buffer16BitView[0] = 1000;

console.log(buffer16BitView[0] + 1);

```

---

Обратите внимание, что доступ к одному представлению не прекращается при использовании другого:

```javascript=
const buffer = new ArrayBuffer(4);
const buffer8BitView = new Int8Array(buffer); 
const buffer16BitView = new Int16Array(buffer); 

buffer16BitView[0] = 1000;

console.log(buffer8BitView);
console.log(buffer16BitView);
```

---

Доступны следующие представления:
`Int8Array()` - восьмибитное число со знаком
`Uint8Array()` - беззнаковое восьмибитное число
`Uint8ClampedArray()` - беззнаковое восьмибитное число ("зажатое")
`Int16Array()` - шестнадцитибитное число со знаком
`Uint16Array()` - беззнаковое шестнадцитибитное число
`Int32Array()` - 32-битное число со знаком
`Uint32Array()` - беззнаковое 32-битное число
`Float32Array()` - 32-битное вещественное число
`Float64Array()` - 64-битное вещественное число

---

Чтоб понять разницу между `Uint8Array` и `Uint8ClampedArray` давайте проведем следующий опыт:

```javascript=
const buffer = new ArrayBuffer(2);
const notClampedBufferView = new Uint8Array(buffer);
const clampedBufferView = new Uint8ClampedArray(buffer);


console.log('Step 1');
notClampedBufferView[0] = 100;
clampedBufferView[1] = 100;
console.log(notClampedBufferView[0]);
console.log(clampedBufferView[1]);
console.log('-----');

console.log('Step 2');
notClampedBufferView[0] += 100;
clampedBufferView[1] += 100;
console.log(notClampedBufferView[0]);
console.log(clampedBufferView[1]);
console.log('-----');

console.log('Step 3');
notClampedBufferView[0] += 100;
clampedBufferView[1] += 100;
console.log(notClampedBufferView[0]);
console.log(clampedBufferView[1]);
console.log('-----');
```

---

Что произошло?
Почему такая разница?

> В первом случае при переполнении переменной, произошёл ее сброс на ноль, во втором случае значение переменной было "зажато" между 0 и 255

---

Не стоит забывать, что в ArrayBuffer можно хранить и двоичное представление других (нечисловых) данных:

```javascript=
const helloStr = 'Hello, world!';

const buffer = new ArrayBuffer(helloStr.length);
const bufferView = new Uint8Array(buffer);


for (let i = 0; i < bufferView.length; i += 1) {
  bufferView[i] = helloStr.charCodeAt(i);
}

for (let i = 0; i < bufferView.length; i += 1) {
  console.log(String.fromCharCode(bufferView[i]));
}
```

---

# Заключение

Итак, на этой лекции были рассмотрены следующие инструменты:
1. Регулярные выражения
2. Объект `Math`
3. Объект `ArrayBuffer`

---

## **Интересное чтиво**

Регулярные выражения:
* [Шпаргалка по регулярным выражениям](http://website-lab.ru/article/regexp/shpargalka_po_regulyarnyim_vyirajeniyam/)
* [regex101.com - проверить свою регулярку](https://regex101.com)
* [MDN - сводка по RegExp](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/RegExp)
* [MDN - Руководcтво по JavaScript: Регулярные выражения
](https://developer.mozilla.org/ru/docs/Web/JavaScript/Guide/Regular_Expressions)
* [Пример, так делать НЕЛЬЗЯ](https://blog.codinghorror.com/regex-use-vs-regex-abuse/)
* [Обзор новшеств ES2016-ES2018](https://habr.com/company/ruvds/blog/353174/)

Math:
* [MDN - Math
](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Math)

ArrayBuffer:
* [MDN - Типизованные массивы JavaScript](https://developer.mozilla.org/ru/docs/Web/JavaScript/Typed_arrays)
* [Habr - ArrayBuffer и SharedArrayBuffer](https://habr.com/company/ruvds/blog/331760/)

---

## Спасибо за внимание. Жду ваших вопросов.