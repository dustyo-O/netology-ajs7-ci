---
title: Синтаксические конструкции
slideOptions:
  transition: slide
---

# AJS. Синтаксические конструкции
<!-- для лектора: Внимание! Для проведения лекции
требуется скачать проект, который рассматривается в ней -->
###### tags: `netology` `advanced js`

![](https://i.imgur.com/44ufSky.jpg)

---

## План занятия
1. Зачем перехватывать ошибки?
2. Зачем генерировать свои ошибки? И как правильно решить - когда и какие?
3. Зачем нужны стрелочные функции, почему просто нельзя использовать обычные?
4. Зачем использовать delete?
5. Зачем нужны замыкания?

---

## Легенда
После обучения Вы устроились на стажировку в небольшую it-компанию. 
До Вас стажировку проходил на этом месте другой стажер, который не смог завершить небольшой проект.
Ваш куратор поручил Вам разобраться с его кодом.

---

## Приступим!
![](https://i.imgur.com/NY7x9R7.jpg)
> Ну что ж, вроде не все так плохо - что-то все-таки есть. А что в консоли?
> ![](https://i.imgur.com/fjLpo8q.png)
> js/game.src.js:86 - ссылается на несуществующий объект
> Видимо, можно продолжить выполнение кода независимо от того, существует ли этот объект, далее.

## Зачем перехватывать ошибки?
Как бы хорошо не был написан код, он не застрахован от ошибок. Обычное поведение программы - в случае ошибки сообщить о ней и окончить выполнение дальнейшего кода.
Однако, бывают ситуации, когда требуется иное поведение.

---

## Зачем перехватывать ошибки?

Если Вы скажете, что ошибки надо решать, а не прятать, я полностью с Вами соглашусь. 

Однако, всегда ли наличие ошибки зависит от Вас?

Может ли быть ситуация, при которой может произойти ошибка, несмотря на то, что написанный Вами код полностью корректен?
> ответы слушателей

---

Например:
* нельзя гарантировать, что необходимые данные получены с сервера;
* нельзя гарантировать, что сторонний сервис всегда доступен и корректно работает;
* нельзя гарантировать, что автор библиотеки (или тот, кто использует Вашу библиотеку) так же добросовестно пишет код, как и Вы;
* нельзя гарантировать, что пользователь не сможет ввести некорректные данные (хоть и надо к этому стремиться).

---

## Зачем перехватывать ошибки?
### Например, при возникновении ошибки необходимо сообщить об этом пользователю как-то культурно
В случае, если пользователь встретится с ошибкой скрипта, лучше, наверное, ему сообщить об этом ошибкой "Что-то пошло не так..." зафиксировать ошибку и дать возможность продолжить работу, а не оставить его один на один с непонятным поведением страницы? 

---

## Зачем перехватывать ошибки?
### Например, при возникновении ошибки необходимо получить бОльшую информацию
При выполнении скрипта возникает ошибка. Не всегда очевидно, какие значения данных к этому приводят. В этом случае в блоке catch достаточно будет дописать вывод необходимых нам данных, приводящих к ошибке.

---

## Зачем перехватывать ошибки?
### Например, случившаяся ошибка не должна прерывать выполнение дальнейшего кода
Подключение виджета для отображение погоды на сайте может прекратить выполнение дальнейшего кода. 
Лучше заранее такое предусмотреть.

---

## Зачем перехватывать ошибки?
Конструкция `try..catch` служит для того, чтобы браузер "попытался" интерпретировать код. 
Однако, если выполнить код не удастся, то можно "поймать" ошибку и/или промежуточные данные, обработать её и затем безопасно выполнять код дальше.

---

## Перехват ошибки
Конструкция `try..catch` состоит из блоков: 
* try
* catch
* finally

---

## try
В блоке try описывается программный код, который браузер должен "попытаться" выполнить.
## catch
В блоке catch описывается программный код, который браузер должен выполнить, если в результате выполнения кода в блоке `try` произошла ошибка.
## finally
В блоке finally описывается программный код, выполнение которого произойдет независимо от того, произойдёт ли ошибка в результате выполнения кода в блоке `try` или нет.

---

## try..catch
```javascript
    try {
        // .. код, который может выполниться неверно
    } catch(e) {
        // .. код, который в этом случае выполнится
    }
```

---

## try..finally
```javascript
    try {
        // .. код, который может выполниться неверно
    } finally {
        // .. код, который выполнится в любом случае
    }
```

---

## try..catch..finally
```javascript
    try {
        // .. код, который может выполниться неверно
    } catch(e) {
        // .. код, который в этом случае выполнится
    } finally {
        // .. код, который выполнится в любом случае
    }
```

---

> js/game.src.js:86
```javascript=86
try {
   this.distanceBox.innerText = Math.ceil(this.distance / 30);
} catch (e) {
   console.log(e);
}
```

--- 

## Ограничения для `try...catch`
Перехват ошибки НЕ СРАБОТАЕТ:
* если имеется **синтакcическая** ошибка;
> демонстрация в консоли:
```javascript
try{
    console.log(Ошибка не произошла!);
}catch(e){
    console.log('Ошибка произошла!');
}
// -> Uncaught SyntaxError: missing ) after argument list
```

В этом случае `try...catch` не будет выполняться, интерпретатор сообщит о синтаксической ошибке

--- 

## Ограничения для `try...catch`
Перехват ошибки НЕ СРАБОТАЕТ:
* если код, в котором произошла ошибка работает **асинхронно** по отношению к ```try...catch```.
> демонстрация в консоли:
```javascript
try {
    setTimeout(()={
        console.log(null.unknown_property);
    },200)
}catch(e){ 
    console.log('Ошибка произошла!');
}
// -> Uncaught ReferenceError: Invalid left-hand side in assignment  
```

---

> добавить в index.html:16 
```htmlmixed=16
<div>Distance: <span id="distance_box">0</span>m</div>
```

---

## Зачем генерировать свои ошибки?
Иногда возникают исключительные ситуации, которые, с точки зрения интерпретатора, ошибкой не считаются, но ошибка проявится позже - например, недополучены какие-то параметры с сервера. 
Или может произойти ошибка со стороны бизнес-логики - например, недопустимое значение исходных данных.
С точки зрения интерпретатора, такие случаи ошибками не считаются, однако, с точки зрения разработччика - они являются ошибками.
В таких случаях требуется генерировать свои ошибки.

---

## throw
Оператор throw создаёт ошибку.
Например, 
> демонстрация в консоли:
```javascript
throw('Я ошибка!');
// -> Uncaught Я ошибка!
```
или
> демонстрация в консоли:
```javascript
a = 1;
b = 1;
c = 1;
d = b ** 2 - 4 * a * c;
if (d<0) throw('При дискриминанте меньше нуля уравнение не имеет вещественных корней!')
console.log('x1 = (-b-Math.sqrt(d))/2/a');
// -> Uncaught При дискриминанте меньше нуля уравнение не имеет вещественных корней!
```
---

## throw и try..catch
Удобно, не правда ли?

Если try...catch служит для перехвата ошибки и позволяет дальнейшее выполнение кода, throw прекращает выполнение кода и создает ошибку.

Используя эти операторы разработчик может контролировать поведение скрипта в отношении ошибок.

---

## Двигаемся дальше
![](https://i.imgur.com/OKkqjEm.png)

---

> Перезапускаем - игра идет нормально, объезжаем конусы, потом нарочито сбиваем один из них.
> Машина проходит сквозь.
> Добавляем в game.src.js:176 console.log(this);
> Очевидно, что это не тот объект, что нас интересует.
> Переносим console.log(this); на строчку выше, там присутствует > нужный нам объект.
> Что же произошло?
> Можно перейти на https://developer.mozilla.org/ro/docs/Web/API/window.setTimeout и прочитать, что: 
> Функция SetTimeout выполняется в другом контексте, отличном от > контекста, в котором задается setTimeout.
> При этом значение this = window, поэтому о передаче правильного this надо позаботиться отдельно.

## Стрелочные функции
В ES6 появилась возможность задания функций через «стрелку» `=>`
Например,
```javascript
let sum = function(a,b){
   return a + b;
}
```
можно записать как
```javascript
let sum = (a,b) => {
   return a + b;
}
```

---

## Стрелочные функции
Если в теле функции не более одной операции, то не обязательно использовать `{}`, при этом для возврата значения не требуется писать `return`.
Например,
```javascript
let sum = function(a,b){
   return a + b
}
```
можно записать как
```javascript
let sum = (a,b) => a + b;
```

---

## Стрелочные функции
### Зачем нужны стрелочные функции?
Стрелочные функции - НЕ "просто короткая запись" обычных функций.
В отличие от стрелочных, обычные функции имеют свой контекст. (свой `this`). 
Стрелочные функции не имеют своего контекста (своего `this`), а берут его из своего окружения.

---

## Стрелочные функции
Например, 
если функцию
```javascript=174
this.car_pos = this.POS_UNDEFINED;
    setTimeout(function(){
      this.car_pos = pos;
    }, 500 / this.speed);
```
заменить на стрелочную:
```javascript=175
this.car_pos = this.POS_UNDEFINED;
setTimeout(() =>
    this.car_pos = pos
, 500 / this.speed);
```
то контекстом `setTimeout` станет нужный нам объект `game`.

> js/game.src.js:175
```javascript=175
setTimeout(() => {
```
> Теперь в this попадает нужный объект, машинка с конусами взаимодействует. Можно убрать console.log
> удалить  в js/game.src.js:176
```javascript=176
console.log(this);
```

---

![](https://i.imgur.com/wjdM7Gg.jpg)

> Машина едет до первого конуса, врезается.
> Запускаем еще раз, гибнем сразу.
> Опять, гибнем сразу
> Выводим game.cones
> Хотя конуса на дороге не видно (так как он удален), его характеристики (объект) все еще хранятся в памяти.

---

## Как происходит удаление объектов в javascript 
Во время выполнения скрипта для каждого примитива или объекта выделяется определенный участок памяти.
Память не бесконечна, поэтому ее требуется периодически очищать от "мусора" - неиспользуемых значений переменных, объектов и их свойств. За этим следит "сборщик мусора" - алгоритм, очищающий память.
Как понять, можно ли удалить какое-то значение? Это просто. Значение считается неиспользуемым, если на него не ведет никакая ссылка.
 
Если мы объявим переменную:
```javascript
x = 'red_car';
```
в памяти будет записано значение `'red_car'`, на которое ссылается указатель `x`
```javascript
console.log(x);
// -> red_car;
```
Можем присвоить еще одному указателю это значение. И этот указатель тоже будет ссылаться на значение `'red_car'`:
```javascript
y = x;
console.log(y);
// -> red_car;
```

---

## Зачем использовать `delete`? 
Сборщик мусора удаляет те значения, на которые не ссылается ни одна ссылка-указатель DOM-дерева. 
Если на значение ведёт ссылка-указатель `x`, значение не будет удалено.
Оператор `delete` удаляет ссылку на значение и позволяет сборщику мусора высвободить память компьютера (если нет других ссылок на значение).

---

## delete

Оператор `delete` позволяет удалять свойства объектов.

Синтаксис:
```javascript
delete nameOfGlobalObjectProperty;
delete object.property;
delete object['property'];
delete array['index'];
```

---

## Delete имеет свои особенности
1. delete возвращает ```false``` только если свойство существует, но не может быть удалено, и ```true``` - в любых других случаях.
> демонстрация в консоли
```javascript
let anybodyObject = {"first": 1};
console.log (delete anybodyObject.second);
// -> true
console.log (anybodyObject);
// -> {first: 1}
```

---

## Delete имеет свои особенности

2. С помощью`delete` можно удалить только свойство объекта, а значит, нельзя удалить переменные (объявленные через `var` и `let`);
> демонстрация в консоли
```javascript
var x = "you can`t delete me";
console.log (delete x);
// -> false
console.log (x);
// -> "you can`t delete me"
```
> демонстрация в консоли
```javascript
function g() {
  let x = 5;
  delete x;
  return x;
}
console.log(g());
// -> 5
```

---

## Delete имеет свои особенности
3. При удалении элемента массива, в массиве сохраняется "пустое место" (`empty`) от этого элемента, то есть длина массива при этом не изменится;
> демонстрация в консоли
```javascript
let array = ["first","second","third"];
console.log (delete array[2]);
// -> true
console.log (array);
// -> ["first", "second", empty]
```

---

## Delete имеет свои особенности

4. delete не изменяет прототип объекта;

5. Существуют свойства, которые нельзя удалить. Например:
```javascript
f = [1,2,'third'];
console.log (delete f.length);
// -> false;
```

---

## Давайте удалим наш конус
```javascript=140
    delete this.cones[key];
```
![](https://i.imgur.com/bYFExzl.png)
> добавить в js/game.src.js:140
```javascript=140
delete this.cones[key];
```

---

> Давайте проверим, работает или нет.
Еще нашли какие-то ошибки?
А кто сколько конусов проехал?
А давайте посчитаем. Как можно посчитать количество проеханных конусов?
> Ответы слушателей

---

## Немного подумаем логически
> Функция removeCone удаляет проеханный нами конус.
> По идее, key - порядковый номер появившегося конуса.
> Получается, что в переменной key у нас уже хранится значение количества конусов, которые мы проехали.

![](https://i.imgur.com/c6Q3Nvv.png)
в переменной `key` в функции `removeCone` у нас уже хранится значение количества конусов, которые мы проехали

> console.log(key)
> Действительно, так и есть.

> index.html:24
```htmlmixed=24
<div class="cone-total">You have traveled <span id="cone_total"></span> cones</div>
```

---

## Освежим теорию
в JavaScript есть 7 базовых типов данных: 
* `undefined`;
* `null`;
* `boolean`;
* `number`;
* `string`;
* `symbol`;
* `object`.

При этом все, кроме `object` считаются примитивными. 
Условно у типа `object` можно выделить «подтипы»: 
* массив (`Array`), 
* функция (`Function`), 
* регулярное выражение (`RegExp`) 
* и другие.

Да-да, функция - это тоже объект.

---

## Освежим теорию 
При запуске функции создается объект `LexicalEnvironment` - дополнительный параметр, который включает в себя аргументы, функции и локальные переменные, которые определяются при вызове функции.

Областью видимости таких параметров является тело функции.

Если внутри функции объявить другую функцию, то она получит доступ к `LexicalEnvironment` внешней.

Как мы уже сегодня говорили, любой объект существует в оперативной памяти, пока есть какой либо указатель, указывающий на него.

---

## Так что же есть замыкание?

Это сохранение лексичекого окружения функции в оперативной памяти после её выполнения с помощью ссылки из объекта, создаваемого при её выполнении.

---

## Так что же есть замыкание?

Например:
```javascript=
function powerOfTwo() {
   let power = 1;
   return function() {
      power *= 2;
      p = power
      return p;
   }
}
```
В данном примере функция, возвращаемая `powerOfTwo()`, использует переменную `power`, которая сохраняет нужное значение между вызовами (вместо того, чтобы сразу прекратить своё существование с возвратом `powerOfTwo`).

Именно поэтому такие функции называют замыканиями. Вложенная функция в примере «замыкает» на себя `LexicalEnvironment` функции, внутри которой определена.

---

## Наше замыкание
```javascript=138
removeConeWrapper() {
  let last = 0;
  this.removeCone = (key) => {
    if (key !== undefined) {
      this.cones[key].remove();
      delete this.cones[key];
      last = key;
    }
    return last;
  };
},
```
> добавим вызов "родительской" функции.
> добавим js/game.src.js: 57
```javascript=50
this.removeConeWrapper();
```

---

## Зачем нужны замыкания?
* Замыкания позволяют сохранять данные функции между ее вызовами;
* Замыкания позволяют создавать функции, генерирующие функции;
* Замыкания позволяют ограничить область видимости переменных.

---

## Итак, подведем итоги
Сегодня нами были освоены новые инструменты:
1. Перехват ошибок
1. Удаление объекта
1. Стрелочная функция
1. Замыкание

---

## Интересное чтиво:

Перехват ошибки:
* https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/try...catch
* https://learn.javascript.ru/exception

Удаление переменной:
* https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/delete
* http://perfectionkills.com/understanding-delete/

Стрелочные функции:
* https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Functions/Arrow_functions
* https://habr.com/post/268795/

Замыкание:
* https://developer.mozilla.org/ru/docs/Web/JavaScript/Closures
* https://habr.com/post/38642/

---

## Copyright

Все изображения (за исключением конуса и взрыва из репозитория) взяты с ресурса [pixabay](https://pixabay.com) и распространяются по лицензии [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

---

## Спасибо за внимание!!! Жду ваших вопросов
![](https://i.imgur.com/LkKlceT.jpg)