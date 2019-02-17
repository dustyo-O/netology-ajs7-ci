---
title: Promises, async/await
slideOptions:
  transition: slide
---

# AJS. Promises, async/await

###### tags: `netology` `advanced js`

---

## План занятия
1. Асинхронный код
1. Promises
1. async/await
1. Тестирование асинхронного кода

---

## Синхронный код

JS исполняет приложения в одном потоке*, т.е. может выполнять одну операцию в единицу времени.

Это нас ограничивает с точки зрения создания современных приложений, особенно связанных с обработкой различных событий.

---

## Синхронный код

```javascript=
const response = getResponse(...);
const data = processResponse(...);
```

Если оба вызова представляют собой быстрые операции, то никаких проблем не возникает.

---

## Длительные операции

Оба вызова могут представлять собой достаточно длительные операции.

Если в это время не обрабатывать другие события, то получится "очередь", т.к. JS будет ждать завершения каждого вызова.

---

## Многопоточность

Многие языки программирования предлагают инструменты для создания и управления несколькими потоками выполнения.

Традиционно, этот раздел считается одним из самых сложных и подверженных ошибкам.

---

## Задача

Перед нами стоит следующая задача: загрузить аналитические данные с сервера и произвести обработку данных на стороне пользователя, выдав ему аналитический отчёт.

Почему на стороне пользователя, а не на сервере?

> Не всегда у нас есть возможность получить доступ к серверу. Возможно, мы используем API Вконтакте для получения этих данных. А разработчики Вконтакте врядли вам дадут написать на их серверах свою аналитическую функцию :smile: 

---

## Асинхронный код

Поэтому в JS присутствует механизм выполнения асинхронного кода, который позволяет "упростить" обработку подобных длительных операций, не прибегая к использованию примитивов многопоточности.

---

## Callbacks

Callbacks - подход, при котором вместо ожидания какого-либо события (например, завершения операции) либо обработки какого-то элемента, мы передаём функцию (callback), которую нужно выполнить после наступления этого события, либо для обработки этого элемента.

```javascript=
function getResponse(args, callback) {
  // где-то внутри функции getResponse
  const response = ...;
  
  callback(response);
}

getResponse(..., (response) => { // наш callback
  ...
});
```

:::info
Важно: понятие callback используется не только в контексте асинхронности. Callback - является функцией, передаваемой в качестве аргумента другой функции, для вызова внутри этой функции. Для Built-in объектов это выполнение каких-либо операций (например, для Array - поиск, сравнение и т.д.).
:::

---

## Callbacks

Определение на MDN звучит следующим образом: "A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action."

---

## Callback Hell

```javascript=
function getResponse(args, callback) {
  // где-то внутри функции getResponse
  const response = ...;
  
  callback(response);
}

function processResponse(args, callback) {
  // где-то внутри функции processResponse
  const data = ...;
  
  callback(data);
}

getResponse(..., (response) => { // наш первый callback
  processResponse(..., (data) => { // наш второй callback
  
  });
});
```

---

## Callback Hell

Нетрудно себе представить, что будет если вызовов у нас будет не 2, а хотя бы 10.

Структура кода превращается в большое количество вложенных вызовов.

Для этого даже придумали отдельный термин - [Callback Hell](http://callbackhell.com).

---

## Ключевое

Среда, в которой будет исполняться ваш JS-код (будь это браузер или Node.js) сама берёт на себя заботу по вызову вашего callback'а в нужный момент времени.

---

## Promises

Использование Promise (обещания) - механизм, позволяющий упростить написание асинхронного кода и решить ряд проблем callback'ов.

---

## Promises

```javascript=
function getResponse(args) {
  // Do something
  return new Promise((resolve, reject) => {
    ...
  });
}
```

Теперь функции не принимают callback для вызова, а возвращают объект класса `Promise`, который и будет играть ключевую роль.

---

## Идея Promise

Ключевая идея `Promise` - это объект, который можем находится всего в трёх состояниях:
* `pending`
* `fulfilled`
* `rejected`

И единственное, что может произойти с `Promise` - это переход из состояния `pending` в состояние `fullfilled` или `rejected`.

Произойти этот переход может только один раз.

---

## Идея Promise

Поскольку функция, выполняющая асинхронную операцию не может вернуть значение этой операции, она возвращает `Promise`, который и "заворачивает" результат выполнения этой операции.

---

## Создание Promise

```javascript=
function getResponse(args) {
  // Do something
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('value');
    }, 500);
  });
}

const responsePromise = getResponse(args);
```

`resolve`, `reject` - функции, вызываемые по завершении операции и переводящие `Promise` в состояние `fullfiled` или `rejected`, соответственно.

---

## then 

Метод, принимающий callback, который должен вызваться в случае перехода `Promise` в состояние `fullfilled`:

```javascript=
const responsePromise = getResponse(args);
responsePromise.then((response) => {
  ...
});
```

---

## Обработка ошибок

При переходе `Promise` в состояние `rejected` вызывается callback, указанный вторым параметром в методе `then`:

```javascript=
const responsePromise = getResponse(args);
responsePromise.then((response) => {
  ...
}, (error) => { // callback for rejected
  ...
});
```

---

## catch

Метод, принимающий callback, который должен вызваться в случае перехода `Promise` в состояние `rejected` или выбрасывания исключения (если оно произошло в коде `then`):

```javascript=
const responsePromise = getResponse(args);
responsePromise.catch((error) => {
  ...
});
```

---

## then + catch

```javascript=
const responsePromise = getResponse(args);
responsePromise.then((response) => {
  ...
}).catch((error) => {
// calback for `rejected` и обработчик ошибок в `then`

});
```

---

## finally

Метод, принимающий callback, который должен вызваться в случае перехода `Promise` в состояние `fullfilled` или `rejected` (вне зависимости от того, в какое состояние перешёл `Promise`):

```javascript=
const responsePromise = getResponse(args);
responsePromise.then((response) => {
  ...
}).catch((error) => {
  ...
}).finally(() => {
  // final actions
});
```

Используется для исключения дублирования кода в `then` и `catch`

---

## Promisification

Использование `Promise` потребовало переписывания старого кода.

Переписывание старого кода (без `Promise`) с использованием `Promise` обозначают термином **Promisification**

---

## Цепочки Promise

`Promise` можно объединять в цепочки, если `then` возвращает тоже `Promise`:

```javascript=
function getResponse(args) {
  // Do something
  return new Promise((resolve, reject) => { ... });
}

function processResponse(response) {
  // Do something
  return new Promise((resolve, reject) => { ... });
}

getResponse(args).then((response) => {
  return processResponse(response);
}).then((data) => {
  // do something
}).catch((error) => {
  // handle error
}).finally(() => {
  // final handlings
})
```

---

## Итоги по Promise

Зачем нужны `Promise`, почему не делать всё на callback'ах?

> 1. Использование `Promise` упрощает работу с асинхронным кодом, помогая избежать Callback Hell
> 1. Современное API написано с использованием `Promise`, поэтому важно уметь использовать этот инструмент
> 1. `Promise` не отменяют callback'и - их всё равно придётся использовать

---

## Итоги по Promise

В каком порядке вызывается `then`, `catch`?

> В том, в котором записаны

```javascript=
const promise = getResponse();
promise.then((data) => {
  throw new Error(); // <- выброс ошибки, сработает следующий по блоку `catch`
}).catch((error) => {
  console.log('first error happened:');
})
.then((data) => {
  console.log(data); // <- сработает `then`
}).catch((error) => {
  console.log('second error happened:'); // <- не сработает
});

// undefined
```

Почему так?

---

## then и catch

Методы `then` и `catch` тоже возвращают `Promise`, благодаря чему возможно построение цепочки `Promise`.

Особенности `then`:
* `then` возвращает `Promise`
* если из `then` возвращается значение, то оно автоматически заворачивается в `Promise`, который переходит в состояние `fullfilled`
* соответственно, если из `then` ничего не возвращается, то в `Promise` кладётся значение `undefined`
* если в `then` выбрасывается ошибка, то ошибка автоматически заворачивается в `Promise`, который переходит в состояние `rejected`
* если из `then` возвращается `Promise`, то последующие вызовы `then` и `catch` будут обрабатывать его состояние

---

## then и catch

Методы `then` и `catch` тоже возвращают `Promise`, благодаря чему возможно построение цепочки `Promise`.

Особенности `catch`:
* `catch` возвращает `Promise`
* если из `catch` возвращается значение, то оно автоматически заворачивается в `Promise`, который переходит в состояние `fullfilled`
* соответственно, если из `catch` ничего не возвращается, то в `Promise` кладётся значение `undefined`
* если в `catch` выбрасывается ошибка, то ошибка автоматически заворачивается в `Promise`, который переходит в состояние `rejected`
* если из `catch` возвращается `Promise`, то последующие вызовы `then` и `catch` будут обрабатывать его состояние

---

## Статические методы Promise

Класс `Promise` содержит ещё ряд статических методов, предоставляющих удобную функциональность:
* `Promise.all(iterable)` - возвращает `Promise`, который переходит в состояние `fullfilled`, только если все `Promise` из `iterable` перешли в состояние `fullfiled` (либо в `iterable` не было `Promise` )
* `Promise.race(iterable)` - возвращает `Promise`, который переходит в состояние `fullfilled` или `rejected` как только любой из `Promise`, содержащихся в `iterable` переходит в `fullfilled` или `rejected`

---

## async/await

С `Promise` всё достаточно хорошо, но есть ли механизмы ещё более упростить этот код?

Ключевые слова `async`/`await` позволяют сделать работу с `Promise` более удобной.

Рассмотрим сразу на примере.

---

## async/await

```javascript=
const response = await getResponse(args);
const data = await processResponse(response);
```

И это вместо:
```javascript=
const promise = getResponse();
promise.then((response) => {
  return processResponse(response);
.then((data) => {
  // Do something
});
```

Удобно?

---

## Обработка ошибок и finally

Здесь тоже всё хорошо, используем конструкцию `try...catch...finally`

```javascript=
try {
  const response = await getResponse(args);
  const data = await processResponse(response);
} catch {
  ...
} finally {
  ...
}
```

---

## async

На использование `await` есть одно ключевое ограничение: `await` можно использовать только внутри `async` функций:

```javascript=
(async () => {
  try {
    const response = await getResponse(args);
    const data = await processResponse(response);
  } catch {
    ...
  } finally {
    ...
  }
})();
```

---

## async

Ключевое слово `async` определяет, что функция выполняется асинхронно - т.е. всегда возвращает `Promise`, но может выглядеть как стандартная функция.

Что значит *как стандартная функция*? Это значит, что если вы просто возвращаете из такой функции значение, то оно заворачивается в `Promise`.

Кроме того, вы можете использовать `await` внутри `async` функции, которое дожидается перехода `Promise` (`await` ставится перед `Promise`) в состояние `fullfilled` или `rejected`.

## Для чего это?

Для упрощения структуры кода, сравним:
```javascript=
(async () => {
  try {
    // ожидаем изменение состояния `Promise`
    const response = await getResponse(args);
    // ожидаем изменение состояния `Promise`
    const data = await processResponse(response);
  } catch {
    ...
  } finally {
    ...
  }
})();
```

```javascript=
getResponse(args).then((response) => {
  return processResponse(response);
}).then((data) => {
  // do something
}).catch((error) => {
  // handle error
}).finally(() => {
  // final handlings
})
```

Первый вариант намного более лаконичный за счёт того, что позволяет избежать нагромождения `then`, `catch`.

---

## async/await

Почему бы тогда совсем не отказаться от `Promise`?

> Потому что в основе работы `async`/`await` лежат `Promise`. `async`/`await` позволяет нам лишь удобнее с ними работать.

---

## Babel

```shell=
$ npm install @babel/polyfill
```

В `.babelrc`:
```json=
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "usage"
      }
    ]
  ]
}
```

---

## Тестирование асинхронного кода

Jest предлагает для всех рассмотренных нами вариантов (callback'и, `Promise`, `async`/`await`) удобные методы для тестирования. Рассмотрим их.

---

## Callbacks

```javascript=
test('should call our callback', (done) => { // <- специальный аргумент
  getData((data) => {
    expect(data).toBe(...);
    done(); // <- указание на завершение теста
  });
});
```

`done` - функция, вызова которой Jest будет ожидать в течение времени, определённого `jest.setTimeout` (по умолчанию - 5 секунд).

Если вызова не будет, получим `FAIL`

![](https://i.imgur.com/TCCl9UF.png)

---

## Promise и async/await

При работе с `Promise` и `async`/`await` достаточно использовать асинхронные тестовые функции (и работать как обычно):
```javascript=
test('should work with promise and async/await', async () => { // <- async
  const data = await getData();
  expect(data).toBe(...);
});
```

---

## Error handling

```javascript=
test('should handle errors', async () => { // <- async
  // <- сообщаем Jest, что у нас один assert, который нужно проверить
  expect.assertions(1);
  try {
    const data = await getData();
  } catch (e) {
    expect(e).toBe(...);
  }
});
```

---

## Итоги

Сегодня мы с вами рассмотрели достаточно много важных вещей:
1. Асинхронный код
1. Promises
1. async/await
1. Тестирование асинхронного кода

---

## Спасибо за внимание! Время задавать вопросы 🙂

---