Промо-виджет Rutube
====================

Промо-виджет - интерактивный рекламный формат для партнеров [видеохостинга Rutube](http://rutube.ru/).

![alt tag](https://github.com/igorpetrovcom/RutubeShowcaseWidget/blob/master/RutubeShowcaseWidget.png)

## Содержание:

1. Описание
2. Технические требования и рекомендации
3. Rutube Widget API
4. Пример виджета

### Описание

Промо-виджет представляет собой рекламную площадь, занимающую область основного контента на страницах Rutube.

Главной особеностью этого рекламного формата является возможность добавлять в него интерактивные элементы, такие как внутренняя навигация (меню), выпадающие элементы, кликабельные изображения, видеоплеер и так далее.

Подготовка промо-виджета требует специальных знаний и навыков - HTML, CSS, JavaScript.

Подготовка и хостинг виджета может осуществляться как самим заказчиком, так и специалистами Rutube. Пример, приводимый в этом репозитории, призван показать как правильно подготовить промо-виджет.

### Технические требования и рекомендации

Технически, промо-виджет - это обычная HTML-страница, которая встраивается на сайт Rutube с помощью тега `<iframe>`.

Требования и рекомендации к странице виджета:

- HTML-страница виджета должна начинаться и заканчиваться тегом `<html>`. Обязательна директива `doctype` в начале документа. Допускается использование технологий HTML5, CSS3, JavaScript;

- работа виджета при любых сценариях не должна вызывать ошибок JavaScript;

- страница должна корректно отображаться в последних версиях основных браузеров для ПК: Chrome, Firefox, IE, Safari, Opera.

- страница должна быть адартирована для отображения на всех современных устройствах, включая планшеты и смартфоны. Это достигается адаптивной либо "резиновой" версткой страницы.

На сайте Rutube используется адаптивная верстка с шириной базовой единицы `248px`. Соответственно, рекомендуемые breakpoints для адаптивной верстки виджета составляют `248px`, `496px`, `744px`, `992px`, `1240px` и `1488px`.

Пример CSS-кода с применением указанных breakpoints в инструкциях [Media Queries](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries):
```css
@media all and (min-width: 1488px) {
	// code for 6+ base units (including Full HD monitors)
}
```
```css
@media all and (min-width: 1240px) and (max-width: 1488px) {
	// code for 5+ base units (including widespread 1366px laptop monitors)
}
```
```css
@media all and (min-width: 992px) and (max-width: 1240px) {
	// code for 4+ base units (including iPad landscape mode)
}
```
```css
@media all and (min-width: 744px) and (max-width: 992px) {
	// code for 3+ base units (including iPad portrait mode)
}
```
```css
@media all and (min-width: 496px) and (max-width: 744px) {
    // code for 2+ base units (various smartphones landscape mode)
}
```
```css
@media all and (min-width: 248px) and (max-width: 496px) {
	// code for 1+ base units (various smartphones portrait/landscape mode)
}
```
```css
@media all and (max-width: 248px) {
	// code for 1 base unit (various smartphones portrait mode)
}
```
- при изменении высоты контентной области страницы (например, при скрытии каких-либо элементов) обязательно оповещение сайта Rutube через использование специального API (см. соответствующий раздел ниже). Также, обязательной является подписка через этот API на некоторые события от Rutube;

- отступы на странице виджета (незаполненная прозрачная контентная область) слева и справа должны составлять `8px` - это особеность для встраивания на сайт Rutube;

- в дизайне виджета желательно использовать шрифт Open Sans. Для использования в CSS его можно подключить через сервис [Google Fonts](http://www.google.com/fonts/);

- суммарный вес страницы с изображениями, файлами и скриптами не должен превышать 5Mb. Изображения должны быть оптимизированы для web и иметь малый вес. Если страница не содержит какого-то сложного интерактива, желательно обходиться без сторонних библиотек вроде jQuery. В целом, нужно стремиться уменьшать вес страницы;

- виджет не должен содержать звуковых эффектов, включающихся без действия пользователя (например, фоновая музыка), однако может проигрывать звук, вызванный осознанными действиями пользователя;

- виджет не должен содержать всплывающих окон, за исключением инструкции `target="_blank"`, которая является обязательной для всех ссылок внутри виджета, ведущих на внешние страницы;

- страница не должна содержать материалов, потиворечащих законодательству РФ или нарушающих авторские права третьих лиц (в том числе изображения).

### Rutube Widget API

Использование тега `<iframe>` в качестве контейнера для виджета имеет как положительные стороны (с точки зрения безопасности), так и несет в себе некоторые неудобства для разработчиков. В частности, тег `<iframe>` не подстраивается под высоту страницы, которую он содержит. Высоту тегу `<iframe>` можно задать лишь в родительском окне, то есть на стороне сайта Rutube. При этом, при кросс-доменном встраивании родительская и дочерняя страницы не могут обращаться к глобальным объектам `document` друг у друга. Для решения этой и других задач был разработан специальный API для работы с виджетом.

Работа API основана на использовании интерфейса [window.postMessage()](https://developer.mozilla.org/en-US/docs/Web/API/Window.postMessage). Страница виджета может как отправлять сообщения сайту Rutube, так и принимать их.

События, которые может отправлять страница виджета, и на которые отреагирует сайт Rutube, должны быть объектами, преобразоваными в строку с помощью метода `JSON.stringify()`.

Шаблон для отправки сообщений:
```javascript
window.parent.postMessage(JSON.stringify({
	type: 'some:event',
	data: 'some data'
}), '*');
```
В настоящий момент виджет может отправить сайту Rutube один тип события:

`framewidget:setHeight` - изменение высоты контента.

Когда на странице виджета изменяется высота контента (например, при скрытии каких-либо элементов), должно быть отправлено это событие. В параметрах должна быть указана высота страницы.

Пример:
```javascript
window.parent.postMessage(JSON.stringify({
	type: 'framewidget:setHeight',
	data: {
		height: 800
	}
}), '*');
```
В свою очередь события, приходящие от сайта Rutube, должны быть преобразованы из строки в объект с помощью метода `JSON.parse()`.

Пример:
```javascript
window.addEventListener('message', function (event) {
	var message = JSON.parse(event.data);
	console.log(message.type); // some type
});
```

Шаблон приходящих сообщений:
```javascript
{
	type: 'event:type',
	data: 'some data'
}
```
События, на которые может подписаться страница виджета:

`framewidget:requestHeight` - событие запроса высоты контента виджета. В любой момент сайт Rutube может запросить высоту страницы виджета, и виджет должен отреагировать отправкой события `framewidget:setHeight`, которое описано выше. Виджет должен быть обязательно подписан на это событие и отвечать на него.

Пример:
```javascript
{
	type: 'framewidget:requestHeight',
	data: {}
}
```
`framewidget:deactivate` - событие, когда область с виджетом становится скрытой на сайте Rutube. Если в данный момент в виджете проигрывается видео или аудио, необходимо их приостановить. Если подобных активных действий в виджете не предусмотрено, событие можно игнорировать.

Пример:
```javascript
{
	type: 'framewidget:deactivate',
	data: {}
}
```
`framewidget:initSocials` - событие с параметрами для инициализации кнопок социального шаринга, если они предусмотрены. В настоящий момент передается url с адресом страницы Rutube, который необходимо расшаривать.
```javascript
{
	type: 'framewidget:initSocials',
	data: {
		href: 'http://rutube.ru/#!/widget'
	}
}
```

### Пример виджета

Данный репозиторий содержит образец готового виджета с примером реализации всех описанных компонентов.
