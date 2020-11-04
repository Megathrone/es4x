# Hello World

Самое простое приложение, которое можно написать - это `Hello World` в файле `hello-es4x.js`:

```js
vertx.createHttpServer()
  .requestHandler(req => {
    req.response()
      .end('Hello ES4X world!');
  })
  .listen(8080);
```

Вы можете запустить это приложение следующим способом:

```bash
$ es4x hello-es4x.js
```

::: tip
В системах UNIX, скрипты можно сделать выполняемыми и шебанг `#!/usr/bin/env es4x` скриптами, что сделает их
автозапускаемыми. При этом учтите, что зависимости уже должны быть в текущей рабочей директории.
:::

Во второй консоли:

```bash
$ curl localhost:8080
Hello ES4X world!
```

::: warning
Запуск скриптов с помощью команды `es4x` напрямую может быть полезным для маленьких скриптов, которые зависят только от
`vertx`. Для более сложных приложений следует использовать менеджер проекта и пакетов.
:::

## Создаем новый проект

ES4X использует инструмент для управления проектом `npm`, для создания нового проекта используется утилитарная команда:

```bash
# создаем директорию проекта
mkdir myapp

# переходим в директорию проекта
cd myapp

# создаем проект
es4x project
```

Проект - это файл `package.json` с некоторыми предварительно настроенными опциями:

```json{7-9,11-17}
{
  "version" : "1.0.0",
  "description" : "This is a ES4X empty project.",
  "name" : "myapp",
  "main" : "index.js",
  "scripts" : {
    "test" : "es4x test index.test.js",
    "postinstall" : "es4x install",
    "start" : "es4x"
  },
  "dependencies": {
    "@es4x/create": "latest",
    "@vertx/unit": "latest"
  },
  "dependencies": {
    "@vertx/core": "latest"
  },
  "keywords" : [ ],
  "author" : "",
  "license" : "ISC"
}
```

::: tip
Для проектов `TypeScript`, запустите команду создания проекта с опцией: `es4x project --ts`
:::

Хук `post-install` поручает es4x разрешить `maven` зависимости и создать скрипт `es4x-launcher`.

::: tip
Скрипт `es4x-launcher` обеспечивает запуск приложений через es4x, а не `nodejs`. Этот скрипт может быть использован в
продакшне, где вы можете отказаться от пакета `@es4x/create`.
:::

### create-vertx-app

С помощью `create-vertx-app` вы можете быстро загрузить ES4X TypeScript или JavaScript приложения в несколько нажатий
клавиш. Если вы предпочитаете создавать приложения через GUI, может быть использован такой генератор, как
[PWA](https://vertx-starter.jetdrone.xyz/#npm).

<asciinema :src="$withBase('/cast/es4x-ts.cast')" cols="80" rows="24" />

## Добавляем зависимости

Добавление зависимостей не отличается от привычного для `JavaScript` разработчиков:

```bash
# добавляем другие зависимости...
npm install @vertx/unit --save-dev # OR yarn add -D @vertx/unit
npm install @vertx/web --save-prod # OR yarn add @vertx/web

# запустит загрузку зависимостей npm + java
npm install
```

## Пишем код

Когда настройки проекта завершены, можно начинать писать код. Как было отмечено ранее, ES4X использует определения
`TypeScript`, чтобы сделать процесс разработки приятнее с помощью автозавершения кода и опциональных проверок типов.

Для всех приложений ES4X доступен глобальный объект `vertx` - настроенный экземпляр *vert.x*, который может быть
использован приложением.

::: tip
Чтобы завершение кода было доступно в [Visual Studio Code](https://code.visualstudio.com/), первая строка вашего
главного скрипта должна быть такой:

```js
/// <reference types="es4x" />
```
:::

Примитивный `index.js` должен быть таким:

```js{1-2}
/// <reference types="es4x" />
// @ts-check
import { Router } from '@vertx/web';

const app = Router.router(vertx);

app.route('/').handler(ctx => {
  ctx.response()
    .end('Hello from Vert.x Web!');
});

vertx.createHttpServer()
  .requestHandler(app)
  .listen(8080);

console.log('Server listening at: http://localhost:8080/')
```

Данное приложение запускает сервер и прослушивает порт 8080 в ожидании подключений. Приложение будет отвечать
"`Hello from Vert.x Web!`" всем запросам на корневой URL (`/`). Для запросов по другим путям приложение ответит
**404 Not Found**.

::: warning
Синтаксис модуля ES6 может быть использован в файлах `.js`. ES4X переведет команды `commonjs` `require()`, однако
команды `exports` не будут переведены. Эта возможность нужна только, чтобы улучшить работу с IDE, которые могут делать
автоимпорт, например, `Visual Studio Code`.
:::

## Поддержка MJS

ES4X также поддерживает файлы `.mjs`. В  этом случае разрешение модулей не будет использовать `commonjs` `require()`, а
будет использовать нативный загрузчик модулей graaljs.

С graaljs `.mjs` поддержка и `import`, и `export` будет работать, как описано в спецификации ES6.

::: tip
Чтобы включить поддержку `.mjs`, или используйте расширение `.mjs` в ваших файлах `JavaScript`, или запустите приложение
с флагом `-Desm`.
:::

::: warning
Нельзя смешивать `commonjs` и `esm` В одном проекте. Если вы не уверены в выборе, используйте `commonjs`.
:::
