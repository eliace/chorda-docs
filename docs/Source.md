Источник данных по сути является чуть более функциональным вариантом `observer`. `Source` решает следующие задачи:
* Является хранилищем данных
* Позволяет создать подписку на изменения данных (только если данные изменяются через API)
* Позволяет создать описание данных для более простого доступа и внесения изменений
* Ленивое создание вложенных `Source` для иерархических данных

## Свойства

### Простые

```javascript
const data = new Source({}, {
    properties: {
        id: Number,
        name: String
    }
})
```

Для простого объявления свойств можно использовать нативные типы данных вроде `Object`, `Array`, `Boolean` и т.д. На самом деле эти типы не используются и нужны лишь для удобного восприятия модели

### Вычисляемые

Вычисляемые свойства задаются как функции

```javascript
const data = new Source({
    list: ['Apple', 'Grape', 'Orange', 'Avocado']
}, {
    properties: {
        list: Array,
        filteredList: v => v.list.filter(itm => itm[0] == 'A')
    }
})
```

Пересчет значения свойства выполняется при любом обновлении данных


Свойство можно объявить и в расширенном виде
```javascript
const data = new Source({}, {
    properties: {
        id: {
            type: Number
        },
        guid: {
            type: String,
            calc: v => uuid(v.id)
        }
    }
})
```

### Вложенные

```javascript
const data = new Source({}, {
    properties: {
        user: {
            properties: {
                address: {
                    properties: {
                        country: String,
                        region: String
                    }
                }
            }
        }
    }
})
```

### Коллекции

`Source` не делает разницы между `Array` и `Object`, все методы применимы к обоим видам коллекций. 

Метод `$all` создает поток (итератор) элементов коллекции как последовательность вложенных `Source`.


## Доступ к данным

Данные можно изменить через служебные методы, например `$set`, `$add`, `$merge` и т.д.

```javascript
const data = new Source({text: 'Coffee'}) // {text: 'Coffee'}
data.$set('text', 'Tea') // {text: 'Tea'}
data.$merge({cups: 2}) // {text: 'Tea', cups: 2}
```

Можно воспользоваться служебным свойством `$value`

```javascript
const data = new Source(0) // 0
data.$value = 10 // 10
```

## Отслеживание изменений

Компоненты начинают отслеживать изменения данных автоматически при подключении скоупа. Как правило, в явном отслеживании необходимости нет.

```javascript
// нам понадобится цель привязки, чтобы сгруппировать наблюдателей
const target = {}
// обработчик изменений
const onChange = (changeEvent) => {
    console.log('Данные изменились:', changeEvent.data)
}

const data = new Source()
data.$observe(target, onChange)
data.$set(10) // Данные изменились: 10
```

Если мы стали явно наблюдать за изменениями, то прекратить наблюдение тоже придется явно

```javascript
data.$unobserve(target) // все наблюдатели, связанные с target будут удалены
```

### События

События изменений бывает трех видов:
1. `changed` значение источника данных изменилось
2. `init` источник данных подключен к `target`
3. `destroy` источник данных отключен от `target`

## Инициализация

Данные можно проинициализировать через конструктор или через свойство `initial`

### Конструктор

```javascript
const a = new Source(10)
const b = new Source({x: 10, y: 20})
```

В качестве значения можно передать любые данные кроме типа `Source` и его наследников.

### Свойство initial

```javascript
const data = new Source(undefined, {
    initial: () => {return 'Hello'}
})
```

Инициализация данных в случае `initial` происходит при первом обращении

```javascript
// из предыдущего примера
console.log(data.$source) // undefined
console.log(data.$get()) // Hello
```



## Примеси



