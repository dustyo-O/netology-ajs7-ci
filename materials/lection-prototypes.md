---
title: Прототипы, конструкторы

slideOptions:
  transition: slide
---

# AJS Прототипы, конструкторы

###### tags: `netology` `advanced js`

---

## План занятия
1. Конструкторы
2. Использование прототипов
3. Привязка контекста
4. Документирование кода

---

## Конструкторы

---

## Создание объектов через литералы

Предположим нужно создать объект товара на странице, можно использовать  литералы.

```javascript=
const product = {
  price: 340,
  name: 'No name t-shirt',
  category: 't-shirt',
};

if (product.category === 't-shirt') {
  product.sizeTable = SIZE_TABLES.T_SHIRTS;
}
```

---

## Создание объектов через литералы

Через некоторое время при написании корзины, понадобилось создавать объекты товаров.

```javascript=
cart.products.map(rawProduct => {
  const product = {
    price: rawProduct.price,
    name: rawProduct.originalName,
    category: rawProduct.categoryName,
  };
    
  if (rawProduct.categoryName === 't-shirt') {
    product.sizeTable = SIZE_TABLES.T_SHIRTS;
  }
});
```

---

## Нарушение DRY

Видим что логика создания объекта товара дублируется, нарушается принцип DRY ("копипаста").

В дальнейшем, изменение структуры или названия полей будет осложнятся.

Решение: выделяем логику создания объекта продукта в функцию.

---

## Конструктор

Конструктор - функция инкапсулирующая в себе логику создания объектов определенного типа.

```javascript=
function createProduct(price, name, category) {
  const product = {
    price,
    name,
    category,
  };

  if (category === 't-shirt') {
    product.sizeTable = SIZE_TABLES.T_SHIRTS;
  }

  return product;
}

const product = createProduct(340, 'No name t-shirt', 't-shirt');
```

---

## Оператор new

Оператор `new` выполняет роль синтаксического сахара при создании объектов.

Визуально упрощается вызов конструктора:

```javascript
const product = createProduct(340, 'No name t-shirt', 't-shirt');
```

визуально упрощается

```javascript
const product = new Product(340, 'No name t-shirt', 't-shirt');
```

---

## Оператор new

Упрощается конструктор:
```javascript=
function Product(price, name, category) {
  this.price = price;
  this.name = name;
  this.category = category;
    
  if (category === 't-shirt') {
    this.sizeTable = SIZE_TABLES.T_SHIRTS;
  }
}
```

---

## Особенности работы конструкторов с new

Особенности работы функций, вызванных через оператор new:
- создаётся новый пустой объект
- ключевое слово this получает ссылку на этот объект
- функция выполняется
- возвращается this

`this возвращается без явного указания!`

---

## Выводы

> Зачем нужны конструкторы, если я могу использовать объектные литералы?

Чтобы инкапсулировать логику создание объекта определенного класса.

> Зачем нужны конструкторы, если я потом в любое время могу добавить/удалить свойство?

Такой код будет проще читать другому разработчику, вся логика будет сборана в одном месте.

---

## Использование прототипов

---

## Добавление методов

Через некоторое время при написании корзины, понадобилось добавить объектам `Product` метод.

```javascript=
function Product(price, name, category) {
  this.price = price;
  // ...
  this.getShortTitle = () => {
    return `${this.name} - ${this.price}`
  };
}
```

---

## Излишние расходы памяти

Недостаток рассмотренного подхода состоит в том, что если будет множество экземпляров `Product`, приведет к дублированию в памяти метода `getShortTitle`. 

```javascript=
const product1 = new Product(...);
const product2 = new Product(...);

product1.getShortTitle === product2.getShortTitle; // false!
```

Решение: использовать прототипы.

---

## Вспомним о прототипах

Все экземпляры Product нужно создать с единым прототипом.

```javascript=
function Product(price, name, category) {
  this.price = price;
  // ...
}

Product.prototype = {
  getShortTitle: () => {
    return `${this.name} - ${this.price}`
  }
};

const product1 = new Product(...);
const product2 = new Product(...);

product1.getShortTitle === product2.getShortTitle; // true!
```
---

## prototype

Синтаксис `Product.prototype` позволяет назначать прототип объекта, создаваемого через `new Product(...)`

---

## Monkey practice по prototype

Нельзя переопределять в прототипе стандартные методы своими реализациями, например, `toString`.

Может привести к неочевидным ошибкам и сложности дальнейшего поддержания кода другими разработчиками.

Исключение: полифиллы.

---

## Полифиллы 

В случае если браузер устаревший и не поддерживает необходимые методы, например в IE8 не поддерживается getter `firstElementChild` для DOM элементов.

Вы можете написать свою реализацию, обязательно проверив отсутствие нативной реализации.

```javascript=
if (document.documentElement.firstElementChild === undefined) {
  Object.defineProperty(Element.prototype, 'firstElementChild', {
    get: function() {
      // код полифилла
    }
  });
}
```

---

## Проверка принадлежности к классу

Для проверки принадлежности экземпляра к определенному классу существет оператор `instanceof`.


Важно: `instanceof` проверяет всю цепочку прототипов.

Пример использования
```javascript=
const product = new Product(...);

product instanceof Product; // true
```

---

## Проверка наличия свойства у объекта

Для проверки наличия свойства у объекта, а не у его прототипа предусмотрен метод протипа объекта `hasOwnProperty`

Пример использования

```javascript=
product.hasOwnProperty(price); // true
product.hasOwnProperty(getShortTitle); // false
```

---

## Выводы

> Зачем мне прототипы, если я могу прямо в конструкторе добавлять в объект функции (методы)?

Для экономии памяти, при использовании прототипа у всех представителей класса будет один и тот же экземпляр метода.

> Что будет, если я заменю функцию в прототипе одного из ""встроенных"" объектов (объектов стандартной библиотеки)?

Возможно появится не предусмотренное поведение кода, в местах, где вы этого не ожидаете.

> Когда это стоит делать?
Только для реализации полифиллов.

> Когда нужно писать свои методы в прототипе?
Если метод будет один и тот же для всех экзепляров класса.

---

## Привязка контекста

---

## Контекст выполнения функции

`Context` - значение `this`, указывающего на объект, которому "принадлежит" текущий исполняемый код.

---

## "Одалживание метода"

Частой типичной ошибкой при работе с методами объектов является потеря контекста.

В таком случае `this` внутри функции перестает указывать на объект, к которому он привязан. 

```javascript=
const getShortTitle = product.getShortTitle;

getShortTitle(); // Uncaught TypeError: Cannot read property 'name' of undefined
```

---

## Потеря контекста выполнения

В зависимости от способа вызова `getShortTitle`, `this` будет указывать на различные объекты

```javascript=
product.getShortTitle(); // this -> product

const getShortTitle = product.getShortTitle;
getShortTitle(); // this -> глобальный объект (window, если не установлено "use strict"
```

Решение: явная привязка контекста исполнения при помощи `bind`, `call` и `apply`

---

## Методы call

Метод прототипа `function`, вызывающий указанную функцию с привязкой контекста (`this`).

Сигнатура

```javascript
func.call(context, arg1, arg2, ...)
```

Пример использования

```javascript=
const getShortTitle = product.getShortTitle;

getShortTitle(); // this -> window
getShortTitle.call(product); // this -> product

```

---

## Метод apply

Выполняет идентичную с `call` фунцию, различается сигнатурой.

Сигнатура `apply`

```javascript
func.apply(context, [arg1, arg2]);
```

Сигнатура `call`
```javascript
func.call(context, arg1, arg2, ...)
```

---

## Метод bind

Метод прототипа `function`, создающий функцию с привязанным контекстом (`this`).

Сигнатура
```javascript
func.bind(context, arg1, arg2, ...)
```
Важно: `bind` вызывает новую функцию!
```javascript
const getShortTitle = product.getShortTitle;

const getShortTitle2 = getShortTitle.bind(product); // this -> product

getShortTitle === getShortTitle2; // false!
```

---

## Простая реализация bind

Для понимания работы можно рассмотреть простую реализацию bind, через apply.

```javascript
function bind(func, context) {
  return function() {
    return func.apply(context, arguments);
  };
}
```
---

## Выводы

> Что такое `call`, `apply`, `bind` и зачем их использовать?

Методы прототипа `Function` для привязки контекста исполнения к функции.

---

## Документирование кода

---

## Документирование кода

Когда вы работаете в команде, ключевое значение приобретает `knowledge sharing` - распространение знаний между участниками команды.

То же самое происходит, когда вы хотите использовать сторонний фреймворк или библиотеку - наличие информации об использовании играет ключевую роль.

---

## Пиши код так, чтобы не нужна была документация

Это верно, но в большинстве случаев не отменяет необходимости наличия документации. Давайте рассмотрим, какие инструменты есть в мире JS и научимся ими пользоваться.

---

## Doc comments

Большинство систем документирования работают схожим образом и предлагают нам блочные комментарии, часть символов в которых интерпретируется специальным образом:

```javascript=
/**
 * Конструктор пользователя
 * 
 * @param name имя пользователя
 * @param type тип пользователя
 */ 
function User(name, type) {
  this.name = name;
  this.type = type;
}
```

---

## Doc comments

Затем эти комментарии обрабатываются инструментом, для извлечения документации. 

---

## Инструменты

* JSDoc
* ESDoc
* DocumentJS
* другие

Если внимательно приглядеться, то большинство 'тегов' повторяются во всех системах: `@param`, `@return`, `@throws` и т.д.

---

## JSDoc

Установим и настроим JSDoc:

```shell
$ npm install --save-dev jsdoc
```

---

## JSDoc - конфигурация

Создадим конфигурационный файл (`jsdoc.conf.json`):
```json=
{
  "source": {
    "include": ["src/js"]
  },
  "opts": {
    "encoding": "utf8",
    "destination": "./docs/",
    "recurse": true
  }
}
```

:::info
Не забудьте добавить каталог `docs` в `.gitignore` и `.eslintignore`.
:::

---

## JSDoc - генерация

Добавим в `package.json` скрипт:

```json=
  "scripts": {
    ...
    "doc": "jsdoc -c jsdoc.conf.json"
  },
```

![](https://i.imgur.com/bX3xdQT.png)

---

## Итоги

Сегодня мы с вами рассмотрели достаточно много важных вещей:
* Конструкторы
* Прототипы
* Привязку контекста
* Документирование кода

---

## Спасибо за внимание! Время задавать вопросы 🙂

---