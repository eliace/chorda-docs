## Компоненты
Компоненты `components` являются способом управления дочерними объектами через ассоциативный массив, т.е. набор пар ключ-значение. Поэтому `components` является экземпляром базового класса `Object`

```javascript
new Html({
    components: {
        // вложенный компонент label
        label: {
            html: 'label'
        },
        // вложенный компонент content
        content: {
            html: 'div'
        }
    }
});
```

#### Аддитивность    

Опция `components` обладает свойством аддитивности, т.е. каждый новый сеттер не перезаписывает старое значение, а объединяет его с новым. Это означает, что можно добавлять компоненты "на лету" или с помощью примесей
```javascript
const html = new Html({
    components: {
        a: {}
    }
});

console.log(html.components); // {a: {...}}

html.opt('components', {b: {}});

console.log(html.components); // {a: {...}, b: {...}}
```
А как удалить компонент?
```javascript
html.opt('components', {a: false});

console.log(html.components); // {b: {...}}
```
#### Шаблоны компонентов

Опция `components` дает нам возможность добавлять и удалять компоненты, но гораздо чаще нам необходимо лишь включать и выключать компонент, что связано с активацией заданного состояния. Здесь нам помогут шаблоны компонентов. Они задаются опцией с ключом компонента и префиксом `$`
```javascript
const html = new Html({
    // шаблон компонента label
    $label: {
        html: 'label'
    },
    // шаблон компонента content
    $content: {
        html: 'div'
    }
})

console.log(html.components) // {label: {...}, content: {...}}
```
По умолчанию все шаблоны активны, т.е. конструктор создаст их даже если не указана опция `components`. Если мы не хотим, чтобы какие-то компоненты активировались, это необходимо указать явно.
```javascript
new Html({
    $header: {},
    $content: {},
    $footer: {},
    components: {
        footer: false // подвал не создаем
    }
});
```
При использовании шаблонов, опция `components` начинает играть роль активатора. Причем, это не означает, что, в качестве значений активаторов могут выступать только `true` или `false`. Если мы укажем активатором опции, они будут объединены с опциями шаблона
```javascript
new Html({
    $input: {
        html: 'input'
    },
    components: {
        input: {
            type: 'checkbox'
        }
    }
});

// после отрисовки мы получим <div><input type="checkbox"></div>
```

<div class="alert is-info">
С помощью шаблонов и активаторов реализуется условное конструирование (if-else). 
</div>
<div class="alert is-warning">
Стоит обратить внимание, что условное конструирование выполняется на основе опций родительского компонента, т.к. до момента добавления нового дочернего компонента опции последнего еще неизвестны.  
</div>

## Элементы
Элементы `items` управляются как последовательный набор объектов, список. Отражением `items` является базовый класс `Array`. Как правило это используется, когда количество компонентов заранее неивестно или все объекты создаются по одному шаблону.
```javascript
new Html({
    items: [
        {html: 'button', text: 'Button 1'},
        {html: 'button', text: 'Button 2'},
        {html: 'button', text: 'Button 3'},
    ]
});
```

Элементы не обладают свойством аддитивности
```javascript
const html = new Html({
    items: [{text: 'Coffee'}]
});

console.log(html.items); // [{text: 'Coffee'}]

html.opt('items', [{text: 'Tea'}]);

console.log(html.items); // [{text: 'Tea'}]
```
Для элементов нет аналога шаблонов

<div class="alert is-info">
С помощью элементов реализуюется итерация (for-each)
</div>

## Фабрики
Компоненты и элементы создаются с помощью фабрик `componentFactory` и `itemFactory` соответственно. 

```javascript
new Html({
    itemFactory: function (options, context) {
        if (options.text == '-') {
            return new Html({html: 'br'})
        }
        else {
            return new Html(options, context)
        }
    },
    items: [
        {text: 'Button 1'}, 
        {text: '-'},
        {text: 'Button 2'},
    ]
});
```

<div class="alert is-info">
С помощью фабрик реализуется вариативное конструирование (switch). 
</div>

#### Фабрика по умолчанию
Фабрика по умолчанию использует опцию `defaultComponent` или `defaultItem` для определения общего шаблона

<div class="alert is-info">
Как работает фабрика компонентов вместе с шаблонами и опциями экземпляра?
<p>Порядок очереди опций такой: [defaultComponent] -> [$шаблон] -> [экземпляр]`</p>
</div>

Тип объекта задается опцией `as`. Вынося определение в отдельный класс, мы можем изолировать группу опций, чтобы легко ее подключать или отключать 

```javascript
import { Button, Link } from './components';

new Html({
    defaultItem: {
        as: Button
    },
    items: [
        'Send', 
        'Share', 
        {as: Link, text: 'Leave'}
    ]
});
```
Если бы мы вместо `as: Link` мы использовали `html: 'a'`, то пришлось бы перекрывать все опции, которые есть в `Button`, но которые не нужны для создания ссылки

## Конструкторы
В качестве опций компонента или элемента могут быть указаны значения, тип которых отличен от `Object`:
* `Boolean`
* `String`
* `Number`
* `Function`
* `Promise`

<div class="alert is-info">
Как правило, Boolean, String и Number используются для определения экземпляра, а Function и Promise для шаблонов.
</div>

#### Boolean
Как указывалось выше, `false` отключает компонент, а `true` включает его обратно.

#### String и Number
Особенностью конструктора элементов и компонентов является автоматическое приведение типов `String` и `Number` к форме {text: ...}. Таким образом заметно упрощается конфигурация элементов, которые отличаются только текстовым содержимым.

```javascript
new List({
    items: ['Coffee', 'Tea', 'Milk']
})

// что аналогично

new List({
    items: [
        {text: 'Coffee'}, 
        {text: 'Tea'}, 
        {text: 'Milk'}
    ]
})
```

#### Function
Результатом работы указанной функции ожидается значение типа `Object`, которое будет использовано как набор опций. Функция будет вызвана при конструировании элемента или компонента

```javascript
//module Page1

export default () => {
    return {
        css: 'page'
    }
}

// module App
import Page1 from './Page1'

new Html({
    $page1: Page1,
    components: {
        page1: false // если компонент не отключить, то Page1 будет вызвана здесь
    }
})

html.opt('components', {page1: true}) // в этот момент вызывается Page1
```

#### Promise
Результатом работы, который будет получен через `promise.then()` ожидается набор опций.
