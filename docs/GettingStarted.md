#### Установка

Загружаем пакет ядра
```bash
npm install @chorda/core
```

Устанавливаем библиотеку отрисовки `React` и адаптер к ней
```bash
npm install @chorda/react react react-dom
```

#### Рендерер

Подключаем адаптер в качестве отрисовщика по умолчанию
```bash
import { Config } from '@chorda/core'
import * as ReactAdapter from '@chorda/react'

Config.use(ReactAdapter.Context);
```

#### Компонент
```bash
const app = new Html({
    css: 'application'
});
```

#### DOM
```bash
document.addEventListener('DOMContentLoaded', function () {
    Config.Renderer.append(app, document.getElementById('app'))
});
```