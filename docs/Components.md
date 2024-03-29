
### Что такое компонент?
Особенности компонента:
1. Компонент управляется через опции
2. Состоянием компонента является динамический набор опций
3. Зоной ответственности компонента является управление структурой (skeleton)
4. Компонент является точкой сочленения (joint) контекста и отрисовщика
5. Компонент не выбрасывает собственных событий

## Создание

При создании компонента выполняются следующие действия:
* Подготавливаются опции
* Подключаются отрисовщик и контекст
* Создаются дочерние элементы
* Инициализируется скоуп

Если инициализация скоупа откладыватся, например, если некоторые источники начнинают подгружать данные, то создание компонента не останавливается

## Удаление

При удалении компонента происходит следующее:
* Отключаются источники данных в скоупе
* Удаляются дочерние элементы
* Компонент отключается от дерева компонентов 

Если отключение источников останавливается, то удаление компонента из структуры произойдет только после того, как будут отключены все источники

## Подключение к контексту

## Подключение к отрисовщику

## Обработчики событий

Сами компоненты не являются источниками событий, но в них очень удобно события обрабатывать. Обработчики событий начинаются на `on`, например, `onClick`, `onSelect` и т.д.
В качестве агрументов обработчику передаются собственно событие и скоуп.

```javascript
const html = new Html({
    scope: {
        selected: false
    },
    onClick: function (e, $scope) {
        $scope.selected.$toggle()
    }
})
```
