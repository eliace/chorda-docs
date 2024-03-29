
Пока идея находится в голове, она безупречна, но когда пытаешься ее реализовать и применить, тут и начинается самое интересное. Мне пришлось писать код, и код этот называется Chorda.


Здесь понадобится немного воображения, т.к. мне придется ввести небольшую аналогию. Итак, представим наше приложение деревом:
Корень. Это стор, где хранятся все данные (кстати, вы когда-нибудь слышали о воздушных корнях?)
Ветви. Промежуточные компоненты, уникальные для приложения. Это страницы, блоки, формы и т.п.
Листья. Простые терминальные компоненты вроде полей ввода, кнопок и др.

Проблема корня решается очень просто: реактивное глобальное хранилище (single responsibility bla-bla-bla).
Проблемы листьев вроде как тоже нет, если вас устраивает какая-нибудь существующая библиотека UI компонентов (бесплатная, в ней есть все нужные компоненты, оперативно исправляются баги, новый функционал вносится без изменения API)
Ветви. Это самая большая проблема. Ведь это именно тот код, который нужно написать самому.

Не знаю как у вас, но у меня проблемы возникают со всем деревом :)

Состояние это не только данные, но и представление. Декларативное описывать UI мы умеем уже давно - html находится у самых истоков. С другой стороны с помощью простого html мы можем описать толко одно состояние, для придания вариативности мы можем пойти разными путями, например, расширить декларацию управляющими конструкциями вроде #if, #else, #switch, а можем придумать свой собственный язык разметки или чуть улучшить существующий. У всех этих способов есть одна большая проблема - их тяжело расширять. А значит для создания нового компонента на основе существующего, придется либо изменять грануляцию последнего, либо писать все заново (в лучшем случае выполнить рекомпозицию). Меня заинтересовал вопрос: можно ли расширить представление компонента за счет условной операции merge? В случае html - нет. Если вы используете классы и раздробили компонент на мелкие методы - может быть. А вот если декларативное описание представляет собой конфигурацию, то - да.

Если вы знакомы с Options API, который используется во Vue, в подходе Chorda с первого взгляда вы не найдете больших отличий. Разница возникает только когда, что Chorda использует опции и для создания представления.

```javascript
const html = new Html({
  html: 'div',
  css: 'box',
  $content: {
    html: 'div',
    css: 'box-content',
    text: 'Coffee'
  }
})
```



Разное

Мне снова стали нравиться классы. Chorda не заставляет создавать классы, если они вам не нужны. 

Что не попало

Моделирование стора
Делегирование данных
Делегирование отрисовки
Пул эффектов
Каналы
Сочленение данных
Фабрики компонентов
Динамические коллекции компонентов (потоки)
И др.

При подключении Chorda в существующий проект выявилась интересная особенность. Если продолжать аналогию с деревом, то мне пришлось делать компонент уровня ветвей, т.е. корень (стор) и ветви (виджеты) оставались старыми, а вот сложный композитный компонент был создан новый. Корректно сшить все удалось за счет того, что: 
* Стор не изменяет исходные данные и контекст Chorda становится прозрачным для внешнего store
* React отрисовщик использует существующий VDOM и в итоге формируется единое дерево React элементов
Не думаю, что то, решение, которое у меня получилось на тот момент является идеальным, но оно решало требуемую задачу.

Если вы знаете что такое VDOM и система компонентов React (которая почему-то не работает), то можете сказать - все уже давно придумано, протестировано и много где используется. Но это не совсем так. React только про отрисовку, с помощью state нельзя организовать полноценное управление состоянием. «Используй Flux, Redux, MobX и т.п.» с их помощью можно эффективно и удобно контролировать состояние приложения.

Если мы подключаем компонент, написанный на Chorda в существующий проект, то за счет переиспользования VDOM и "прозрачности" контекста, можно добится эффекта бесшовного встравания не только в терминальных узлах DOM дерева, но и в середине.

В статью не влезло все, что мне хотелось бы рассказать про Chorda, я постарался осветить только самые интересные и, возможно, спорные моменты.

Почему фреймворк? Начав с декларативного описания состояний, я понял, что одно отступление от DX тянет за собой другое и уместить все в одной узкоспециальной библиотечке не удается.

Когда библиотека была готова, оказалось, что с использованием предалагаемых ей подходов для разработки целого приложения оказалось проблемой. Приложение, конечно, написать было возможно, представляя его одним большим компонентом, но необходимость прикручивать роутер или держать под контролем сложное состояние приводило к возникновению все новых абстракций и чрезмерному усложнению кода.