---
title: Классы и наследование
slideOptions:
  transition: slide
---

# AJS. Классы и наследование

###### tags: `netology` `advanced js`

---

## План занятия
1. Иерархия наследования
1. Классы
1. Конструкторы
1. Наследование
1. Статические методы

---

## Повторение

На предыдущих лециях мы рассмотрели с вами:
* цепочки прототипов и свойство `__proto__`
* функции конструкторы и свойство `prototype`

---

## Наследование

Наследование (Inheritance) - механизм, подразумевающий переиспользование свойств "родительского объекта" или "родительского класса" в дочернем.

При этом выделяют два ключевых вида наследования:
* на базе прототипов (на базе объектов)
* на базе классов

---

## Наследование в JS

Наследование в JS строится на базе цепочки прототипов (уже рассмотренного нами механизма). Т.е. любое свойство сначала ищется в самом объекте, потом в прототипе объекта, потом в прототипе прототипа и т.д.

---

## Задача

Перед нами встала задача организовать веб-мессенджер.
В базовой версии он должен позволять обмениваться сообщениями только пользователям, зарегистрированным в нашей системе.
А затем мы хотим подготовить специализированные версии, которые позволят общаться с пользователями других мессенджеров, например, Viber.

---

## Old Style

---

## Функция конструктор

```javascript=
function Messenger(name) {
  this.name = name;
}
```

---

## Метод в прототипе

```javascript=
Messenger.prototype = {
  send(recipient, msg) {
    // TODO: send text message
  },
};
```

---

## Специализация

Первое, чего мы хотим добиться, чтобы у каждого специализированного мессенджера были в наличии все те же свойства, что есть и в базовом.

---

## Наследование свойств

```javascript=
function Messenger(name) {
  this.name = name;
}

function MultiMessenger(name) {
  Messenger.call(this, name); // <-
}
MultiMessenger.prototype = Object.create(Messenger.prototype);
MultiMessenger.prototype.constructor = MultiMessenger;

const viber = new MultiMessenger('Viber');
console.log(viber.name); // Viber
```

---

## Object.create

Метод, позволяющий создать новый объект с установленным объектом прототипа. Фактически, мы в свойство `prototype` нашей функции конструктора прописываем объект, у которого в прототипе будет свойство `prototype` из `Messenger`.

---

## Специализация

Второе, нужно иметь возможность добавлять собственные свойства.

---

## Добавление свойств

```javascript=
function Messenger(name) {
  this.name = name;
}

function MultiMessenger(name, logo) {
  Messenger.call(this, name);
  this.logo = logo; // <-
}
MultiMessenger.prototype = Object.create(Messenger.prototype);
MultiMessenger.prototype.constructor = MultiMessenger;

const viber = new MultiMessenger('Viber', 'V');
console.log(viber.name); // Viber
console.log(viber.logo); // V
```

---

## Методы

Посмотрим, что с методами.

---

## Наследование методов

```javascript=
function Messenger() { ... }
Messenger.prototype = {
  send(recipient, msg) {
    // TODO: send text message
  },
};

function MultiMessenger() { ... }
MultiMessenger.prototype = Object.create(Messenger.prototype);
MultiMessenger.prototype.constructor = MultiMessenger;

const viber = MultiMessenger();
viber.send('...');
```
---

## Методы

Теперь нужно добавить свои, так, чтобы можно было посылать сообщения пользователям Viber.

---

## Переопределение метода

Можем ли мы переопределить метод `send` в `MultiMessenger`?

```javascript=
function Messenger() { ... }
Messenger.prototype = {
  send(recipient, msg) {
    // TODO: send text message
  },
};

function MultiMessenger() { ... }
MultiMessenger.prototype = Object.create(Messenger.prototype);
MultiMessenger.prototype.send = function(recipient, msg) { ... };
MultiMessenger.prototype.constructor = MultiMessenger;

const viber = MultiMessenger();
viber.send('...');
```

---

## Методы

Стоп, но тогда мы уже не сможем отправлять сообщения пользователям нашего сервиса. Как это исправить?

---

## Вызов родительского метода

```javascript=
function Messenger() { ... }
Messenger.prototype = {
  send(recipient, msg) {
    // TODO: send text message
  },
};

function MultiMessenger() { ... }
MultiMessenger.prototype = Object.create(Messenger.prototype);
MultiMessenger.prototype.send = function(recipient, msg) {
  if (<recipient is from our service>) {
    Messenger.prototype.send.call(this, recipient, msg);
    return;
  }
  
  // esle send via Viber
};
MultiMessenger.prototype.constructor = MultiMessenger;

const viber = MultiMessenger();
viber.send('...');
```

---

## ES6

Манипуляция прототипами позволяет добиться нужного уровня гибкости, но в большинстве случаев является избыточной.

ES6 принёс нам ключевые слова `class` и `extends`, позволяющие использовать аналогичные другим языкам конструкции для создания функций-конструкторов и цепочек прототипов.

---

## class

Удобная форма или "синтаксический сахар", позволяющий объединить создание функции-конструктора и добавление функций в прототипы.

---

## class

```javascript=
class Messenger {
  constructor(name) { // Аналог функции конструктора
    this.name = name;
  }
  
  send(recipient, msg) { // Аналог .prototype.send
  
  }
}

const messenger = new Messenger('...');
```

---

## constructor

`constructor` не является обязательным, вы можете не создавать его, если он вам не нужен.

---

## Поля

Почему нельзя написать поле выше конструктора, как в других языках (не писать `this.field`)

> Данный синтаксис пока поддерживается не во всех браузерах (и, вероятно, будет в стандарте ES2019)

Есть ли инкапсуляция как в других языках?

> На данный момент предполагается, что эта возможность появится в ES2019.

---

## class

Важные моменты:
```javascript=
console.log(typeof Messenger); // function
console.log(Messenger);
```

![](https://i.imgur.com/sLpzqX1.png)


---

## Особенности

1. Все методы - не перечисляемы:
![](https://i.imgur.com/w2Kj6Gw.png)
1. Нельзя использовать без `new`:
![](https://i.imgur.com/LD4SHIs.png)
1. Нельзя переопределить `prototype`: 
![](https://i.imgur.com/dInppaY.png)

---

## Зачем нужны классы?

Зачем нужны классы, если есть функции-конструкторы и прототипы?

> Во-первых, классы позволяют писать более лаконичный код.
> Во-вторых, это современный стиль написания JS-кода.

---

## Функции-конструкторы и цепочки прототипов

Как теперь быть с цепочками прототипов и функциями конструкторами - их больше нет?

> Они по-прежнему остались и работает, но скрыты от нас "синтаксическим сахаром"

---

## Advanced

Вопрос:
> А что, если я хочу использовать тонкую настройку свойств через `Object.defineProperty`?

Ответ:
> Это можно сделать в конструкторе.

---

## extends

Позволяет организовать наследование:

```javascript=
class MultiMessenger extends Messenger { }

const viber = new MultiMessenger('viber');
console.log(viber.name); // viber
```

Все существующие свойства уже "наследуются".

---

## Для чего нужно наследование

Для чего нужно наследование, я ведь могу просто создавать нужные мне классы?

> Для переиспользования кода и построения иерархий
> Наследование не всегда является хорошим решением, но это вопрос архитектуры

---

## Добавление свойств

```javascript=
class MultiMessenger extends Messenger {
  constructor(name, logo) {
    super(name); // <- Messenger.call(this, name): вызов конструктора родителя
    this.logo = logo;
  }
}
```

---

## super()

`super()` можно использовать только в конструкторе и только первым вызовом.

---

## extends без constructor

На самом деле
```javascript=
class MultiMessenger extends Messenger { }
```

Эквивалентно:
```javascript=
class MultiMessenger extends Messenger {
  constructor(...params) {
    super(...params);
  }
}
```

---

## Переопределение метода

```javascript=
class MultiMessenger extends Messenger {
  send(recipient, msg) {
    // TODO: send message
  }
}
```

---

## Вызов родительского метода

```javascript=
class MultiMessenger extends Messenger {
  send(recipient, msg) {
    if (<recipient is from our service>) {
      super.send(recipient, msg);
      return;
    }
  }
}
```
---

## super

`super` позволяет получать доступ к свойствам и методам "родителя". Причём не важно, как этот самый родитель был установлен:

```javascript=
const basic = {
  send(msg) {
    // TODO: send message
  },
};

const viber = {
  send(msg) {
    super.send(msg);
  },
};

Object.setPrototypeOf(viber, basic);
viber.send('hello from viber');
```

---

## Статические методы

До ES6 статическими методами назывались методы, добавленные в функцию-конструктор:
```javascript=
function Image() { ... }
Image.from = function(...) { ... };
```

В ES6 для этого используется ключевое слово `static`
```javascript=
class Image {
  static from(...) {
    ...
  }
}
```

---

## Другие возможности классов

* get/set
* передача в функции
* свойства с вычисляемыми именами
* и т.д.


---

## ES6 Style

На сегодняшний день использование классов является предпочтительным

---

## Хранение классов

Где хранить определения классов - в отдельных файлах или прямо в основном файле приложения?

> Это вопрос к организации кода. Поскольку мы используем сборщик (Webpack), то требуем от вас хранения отдельного класса (либо группы связанных классов) в отдельном модуле.

---

## Итоги

Сегодня мы с вами рассмотрели достаточно много важных вещей:
1. Иерархия наследования
1. Классы
1. Конструкторы
1. Наследование
1. Статические методы

---

## Спасибо за внимание! Время задавать вопросы 🙂

---