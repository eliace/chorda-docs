Домен является расширенным вариантом источника данных, добавляя к нему возможности создавать и обрабатывать события, а также контролироваться связанные с ними эффекты

`Source` вполне достаточно, если вы работате с примитивами, сложные данные требуют более сложного описания и способов управления ими

## События

`Domain` в отличие от `Source` позволяет создавать собственные события

```javascript
const data = new Domain(false, {
    events: {
        select: {}
    }
})

data.select()

```

## Подписки

Подписка на событие без, собственно, определения события достаточно редкая вещь

```javascript
const data = new Domain(false, {
    listeners: {
        select: function (k) {
            this.selection = k
        }
    }
})
```

## Действия

Чаще всего событие и слушатель объявляются вместе. Такая пара называется `action`

```javascript
const data = new Domain({}, {
    actions: {
        sum: function (a, b) {
            this.$value = a + b
        }
    }
})

data.sum(5, 3) // 8

```

## Наблюдатели

В широком смысле наблюдение осуществляется за событиями, появлющимися в домене. В узком смысле наблюдается изменение свойства домена.

```javascript
const data = new Domain({}, {
    watchers: {
        foo: {
            when: (evt) => evt.name == 'foo',
            callback: function (evt) {
                this.loadRemoteData()
            }
        },
        isVisible: function (v) {
            this.$emit(v ? 'opened' : 'closed')
        }
    }
})
```

## Эффекты

 Эффект это оберкта над промисом, которая ведет себя как промис. Эффекты возникаю, когда слушатель события или наблюдатель возвращает промис или другой эффект.

 ```javascript
 const data = new Domain({}, {
    actions: {
        load: function () {
            return fetch('http://mydomain/api/list')
        }
    }
 })

const eff = data.load() // возвращает эффект

eff.then((v) => {
    console.log(v) // значени выводится в консоль после загрузки данных
})

 ```

Можно создавать свои эффекты, расширяя класс `Effect`. 

Какой именно эффект будет создаваться, мы можем указать при отправке или объявлении события.

```javascript

class MyEffect extends Effect {
    // что-то полезное
}

data.$emit('myEvent', null, {effect: MyEffect})
```