# appshell-core

Ядро оболочки MiniAppHub. Не содержит пользовательского интерфейса и не собирается в приложение —
это рантайм, мост API и набор действий, которые используют `appshell-mobile` и `appshell-desktop`.

## Состав
```
index.html      страница оболочки: единственный iframe-песочница, ничего больше
tokens.css      дизайн-токены, которые наследуют мини-приложения
core/runtime.js чтение config.json, выбор лаунчера, мост postMessage
core/store.js   конфиг, настройки, реестр установленных приложений
core/bus.js     канал «действие API ↔ рантайм»
core/api/       действия: один файл — одно действие, default-экспорт
core/sdk.js     клиент для мини-приложений: mapp.shell('getSettings')
```

## Модель API
Мини-приложение вызывает действие по имени, а оболочка **динамически** подгружает файл:

```js
import { mapp } from './api/sdk.js';

const s = await mapp.shell('getSettings');            // → core/api/shell/getSettings.js
const t = await mapp.shell('setSettings', { theme: 'light' });
```
Нет `index.js`, который экспортирует всю категорию: каждое действие лежит в отдельном файле
и экспортирует одну функцию по умолчанию. Платформа может переопределить любое действие,
положив свой файл в `api/<namespace>/<action>.js` — рантайм ищет сначала там, потом в ядре.

## Разрешения
Из `permissions` манифеста приложения: `apps.manage` для установки и удаления,
`shell.launcher` для смены лаунчера и запуска приложений. Странице каталога платформы
установка разрешена без манифеста — по совпадению origin с `catalogUrl`.

## Тесты
```bash
npm test        # node --test, 9 проверок диспетчера, прав и настроек
```

Используется как git submodule в `appshell-mobile` и `appshell-desktop`.
