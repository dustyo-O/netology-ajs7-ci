---
title: Модули
slideOptions:
  transition: slide
---

# AJS. Модули

###### tags: `netology` `advanced js`

---

## План занятия
1. Зачем нужны модули ES
2. Экспорт: export, export default
3. Импорт: import, смешанный импорт, import as, import * as
4. Основные концепции webpack и его конфигурационный файл


---

## Зачем нужны ES модули?
- сложность JavaScript приложений очень сильно выросла с 2006г, когда появился jQuery: появилась сложная бизнес-логика на клиенте и необходимость управлять большим количеством данных, и как следствие - файлами различных типов
- если раньше было достаточно вручную при помощи тега script подключить несколько скриптов на HTML-страницу и легко следить за их зависимостями, то современное приложение при таком подходе невозможно поддерживать и расширять

---

## Устаревший подход к модулям

* паттерн проектирования “модуль” (может быть немедленно вызываемой функцией или функцией-конструктором)
* jQuery плагины
* JavaScript в стиле ООП

---

## Проблемы при устаревем подходе

- конфликты имен модулей в глобальной области видимости
- необходимо вручную следить за последовательностью загрузки и инициализации модулей с учетом зависимостей между ними (для каждой HTML-страницы)

---

## Подходы к организации модулей

* AMD (Asynchronous Module Definition) - одна из первых систем организации модулей
* CommonJS - система модулей, используемая Node.js
 ```javascript=
const students = require('./students.js');  

console.log(students.getGroups());

// в students.js
exports.getGroups = () => {
  // ...
};
```
* UMD (Universal Module Definition) - система модулей, совместимая с системой AMD и CommonJS.
 
---
## Как правильно разбивать код на модули?

Модуль - это файл с кодом, из которого экспортируется хотя бы одна переменная (иначе его нельзя переиспользовать). 

Один класс, компонент или библиотека – один модуль.

---

## Именованный экспорт

ключевое слово export ставится перед объявлением переменных, функций и классов.
```javascript
export const getStudentsByGroup = (students, group) => 
  students.filter(student => student.group === group);
```
В случае экспорта заранее объявленной переменной/функции/класса необходимо взять их название в фигурные скобки:
```javascript=
const getStudentsByGroup = (students, group) => 
  students.filter(student => student.group === group);
    
export { getStudentsByGroup }; // необходимо взять в фигурные скобки
```
Можно экспортировать сразу несколько переменных:
```javascript
export { getStudentsByGroup, getGroupsByCourse }; 
```
---

## Экспорт по умолчанию

с использованием ключевого слова **default**, в одном файле может быть только один дефолтный экспорт:
```javascript
const сourseUtils = { ... };
export default сourseUtils;
```
Можно экспортировать по умолчанию и новый объект: 
```javascript=
export default { 
  getStudentsByGroup, // короткая запись ES6 для getStudentsByGroup: getStudentsByGroup
  getGroupsByCourse,
};
```
---

## Переименование при экспорте (export as)

При помощи ключевого слова **as** можно экспортировать переменную/функцию/класс под другим именем:
```javascript
export { getStudentsByGroup as getStudents, getGroupsByCourse as getGroups };
```
---

## Именованный импорт 

```javascript=
// модуль сourseUtils .js
const сourseUtils  = {...};

export const getStudentsByGroup = (students, group) => 
  students.filter(student => student.group === group);
    
export getGroupsByCourse = (groups, course) => 
  students.filter(group => group.course === course);
    
export default сourseUtils;
```

Импорт одной или нескольких переменных, функций или классов по имени:
```javascript
import { getStudentsByGroup, getGroupsByCourse } from '/сourseUtils.js';
```
Имя при эспорте и при импорте в данном случае должно совпадать. 

---

## Импорт значения по умолчанию

```javascript=
// модуль сourseUtils .js
const сourseUtils  = {...};

export const getStudentsByGroup = (students, group) => 
  students.filter(student => student.group === group);
    
export getGroupsByCourse = (groups, course) => 
  students.filter(group => group.course === course);
    
export default сourseUtils;
```

```javascript
import сourseUtils from '/сourseUtils.js';
```
имя может быть и другим, не обязательно сourseUtils

---

## Смешанный импорт

Имспорт по умолчанию и по имени в одной строке

```javascript=
import сourseUtils, { getStudentsByGroup, getGroupsByCourse } from '/сourseUtils.js';
// вместо:
import сourseUtils from '/сourseUtils.js';
import { getStudentsByGroup, getGroupsByCourse } from '/сourseUtils.js';
```
---

## Импорт всего содержимого модуля

**import * as**, при этом необходимо дать модулю имя
```javascript
import * as utils from '/сourseUtils.js';
```
Обращаться к конкретным переменным через utils:
```javascript
const { getStudentsByGroup, getGroupsByCourse, сourseUtils } = utils;
```

---

## Импорт с переименованием (import as)

Если после импорта переменной хочется обращаться к ней по имени, отличающимся от того, с которым ее экпортировали:

```javascript
import { getStudentsByGroup as getStudents, getGroupsByCourse as getGroups } from '/сourseUtils.js';
```

---
## Как в другом модуле импортировать объект courseUtils?

```javascript=
// модуль courseUtils.js
const courseUtils = {...};

export const sortStudentsByMark = students => (
  students.sort((a, b) => { 
    if (a.mark > b.mark) {
      return 1;
    }
    if (a.mark < b.mark) {
      return -1;
    }
    return 0;
  })
);

export const getBestStudent = students => sortStudentsByMark(students)[0];

export default courseUtils;
```
**Варианты:**
1. ```import { courseUtils } from '/courseUtils.js';```
2. ```import courseUtils from '/courseUtils.js';```
3. ```import * from '/courseUtils.js';```
4. ```import { default as courseUtils } from '/courseUtils.js';```

---

## Как импортировать функцию sortStudentsByMark под именем sortStudents?
```javascript=
// модуль courseUtils.js
const courseUtils = {...};

export const sortStudentsByMark = students => (
  students.sort((a, b) => { 
    if (a.mark > b.mark) {
      return 1;
    }
    if (a.mark < b.mark) {
      return -1;
    }
    return 0;
  })
);

export const getBestStudent = students => sortStudentsByMark(students)[0];

export default courseUtils;
```
**Варианты:**
1. import * as sortStudents from '/courseUtils.js';
2. import sortStudentsByMark as sortStudents from '/courseUtils.js';
3. import { sortStudentsByMark as sortStudents } from '/courseUtils.js';

---

## Как обратиться к функции getBestStudent?
```javascript=
// модуль courseUtils.js
const courseUtils = {...};

export const sortStudentsByMark = students => (
  students.sort((a, b) => { 
    if (a.mark > b.mark) {
      return 1;
    }
    if (a.mark < b.mark) {
      return -1;
    }
    return 0;
  })
);

export const getBestStudent = students => sortStudentsByMark(students)[0];

export default courseUtils;
```

```javascript
import * as utils from '/courseUtils.js';
```
**Варианты:**
1. utils.getBestStudent()
2. getBestStudent()
3. нельзя к ней обратиться

---

## Как использовать модули сейчас?

Поддержка модулей присутствует в свежих версиях популярных браузеров:
```javascript=
<script type="module">
  import { groups } from './jsCourse.js';
  console.log(groups);
</script>
```
Или вот так можем сказать браузеру, что в подгружаемом скрипте используются модули:
```javascript
<script type="module" src="./jsCourse.js"></script> 
```
Для более полной поддержки браузерами рекомендуется использовать транспайлеры, такие как Babel, а также сборщики, например, Webpack.

---

## CommonJS

---

## module.exports

В CommonJS, если мы хотим сделать имя (функцию, переменную либо объект) доступным из нашего модуля, то:
```javascript=
module.exports = {
  variable: { ... },
  method: function() { ... },
};
```

---

## require

Если мы хотим использовать имя, экспортированное из другого модуля, в своём модуле, то:
```javascript=
const mod = require('<path_to_module>');
// mod.variable - доступ к конкретному имени
// mod.method() - доступ к конкретному имени
```

---

## Что использовать?

Придётся использовать обе, т.к. платформа Node.js поддерживает ES Modules в экспериментальном режиме.

---

## Webpack. Основные концепции

Webpack - сборщик пакетов (бандлов).

* **Бандл** (Bundle)
Бадлы состоят из некоторого количества модулей и содержат итоговые версии исходных файлов, которые уже прошли компиляцию.

* **Граф зависимостей** (Dependency graph)
Когда webpack обрабатывает JavaScript приложение, он строит внутренний граф зависимостей, который сопоставляет каждый модуль приложения и генерирует один или несколько бандлов. 
 
* **Точка входа в приложение** (Entry point)
Точка входа указывает какой модуль webpack должен использовать чтобы начать строить свой граф зависимостей. webpack выясняет, от каких модулей и библиотек зависит эта точка входа (напрямую и через зависимости его зависимостей).
По умолчанию точкой входа считается файл `./src/index.js`, но можно указать другой (или несколько других) в конфигурационном файле webpack.

`webpack.config.js`:
```javascript=
module.exports = {
  entry: './path/to/my/entry/file.js',
}
```

---
## Webpack. Выходной файл (Output)


Свойство `output` указывает Webpack куда выводить создаваемые им бандлы и как их назвать. Название по умолчанию для главного выходного файла `./dist/main.js`. 

`webpack.config.js`:
```javascript=
const path = require('path'); // Node.js модуль для разрешения путей файлов

module.exports = {
  entry: './jsCourse/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'jsCourse.bundle.js',
  },
};
```

Свойства `output.filename` и `output.path` указывают имя бандла и куда его сохранить. 

---

## Webpack. Загрузчики (Loaders)

Два основных свойства для настройки загрузчика::
* **test** показывает какие типы файлов должны быть обработаны Webpack
* **use** указывает какой загрузчик должен использоваться для трансформации файлов указанного типа.

`webpack.config.js`:
```javascript=
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js',
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' },
    ],
  },
};
```
Когда в каком-то JavaScript файле встретится `import` файла `txt`, то будет использоваться загрузчик raw-loader для его обработки перед добавлением в бандл (raw-loader выдаст содержимое .txt файла как строку).

---

## Webpack. Плагины (Plugins)

Оптимизация бандлов, управление картинками, HTML файлами и тд.

`webpack.config.js`:
```javascript=
const HtmlWebpackPlugin = require('html-webpack-plugin'); // устанавливается через npm
const webpack = require('webpack'); // для получения доступа ко встроенным плагинам

module.exports = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({ template: './src/index.html' }),
  ],
};
```
плагин html-webpack-plugin генерирует HTML файл и автоматически вставляет в него ссылки на сгенерированные бандлы (чтобы не приходилось это делать вручную)

---

## Webpack. Mode (Режим)

Можно включить встроенную оптимизацию Webpack для конкретного окружения:
**development** (разработка), **production** (продакшн) или **none** (не установлен)

`webpack.config.js`:
```javascript=
module.exports = {
  mode: 'production',
};
```

---

## Webpack. Совместимость с браузерами

Webpack поддерживает все браузеры, совместимые с ES5 (IE8 и ниже не поддерживаются). 
Для поддержки более старых версий необходимо сначала загрузить полифиллы для Promise для использования в `import()` и `require.ensure()`. 

---

## Чему мы научились
- узнали, зачем нужны модули ES и как правильно разбивать код на модули
- использовать экспорт: export, export default
- использовать импорт: import, смешанный импорт, import as, import * as
- использовать модули сейчас
- основным концепциям Webpack и его настройке через конфигурационный файл

---

## Ссылки на дополнительные материалы
1. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import
1. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export
1. https://webpack.js.org/

---

## Спасибо за внимание! Время задавать вопросы 🙂

---