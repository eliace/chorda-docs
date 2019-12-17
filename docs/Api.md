## Html

### opt(key)
* Аргументы

`{String} key` имя опции

* Результат

`{any}` текущее значение опции

* Пример

```javascript
    const styles = html.opt('styles')
```
### opt(key, value)
* Аргументы

`{String} key` имя опции

`{any} value` новое значение опции

* Результат

    нет

* Пример

```javascript
    html.opt('title', 'Всплывающая подсказка')
    html.opt('styles', {color: '#ff00ff'})
```

### opt(options)
* Аргументы

    `{Object} options` набор опций

* Результат

    нет

* Пример

```javascript
    html.opt({
        id: '121',
        name: 'Tea'
    })
```

## Source

### $get(key)

### $set(value)

### $set(key, value)

### $add(value, index)

### $remove(index)

### $toggle(key)

### $entry(key)

### $at(key)

### $each(callback)

### $merge(object)

### $iterator()

### $all()

### $size()

### $observe(target, callback, channel)

### $unobserve(target)

### $clone()

### $snapshot()

### $value
* Тип: `any`
* По умолчанию: нет
* Пример:

```javascript
const html = new Source(5)
html.$value // 5
```

### $source
* Тип: `any`
* По умолчанию: нет
* Пример:

```javascript
const data = new Source({text: 'Coffee'})
const text = html.$at('text')
text.$source // data
```

$insert ?
$removeIf ?
$firstOf ?
$lastOf ?
$isEmpty ?

## Domain

### createAction()

### createEffect()

### createEvent()

### createProperty()

### $subscribe()

### $unsubscribe()

### $watch()

### $on()

### $off()

### $emit()

### $effects
* Тип: `Array`
* По умолчанию: `[]`
* Пример:

```javascript
```

## Config

use

## Stream

filter
map
each