# Markdown

В Markdown документах включена поддержка HTML. Добавлены специальные теги DocHub, которые позволяют
заметно расширить функциональность:

## Навигационные якоря
Позволяет создавать именованные якоря для "коротких" ссылок внутри документа. Например, код:
```Markdown
![](@anchor/markdown)
```
создает якорь "markdown". Теперь на него можно сослаться из любой части документа:
```Markdown
[Пример короткой ссылки](#markdown)
```

## Изображения
Поддерживается встраивание изображений. Например:

```Markdown
![Кот](examples/cat.jpg)
```

![Кот](examples/cat.jpg)

## Ссылки на объекты DocHub
Добавляет ссылку на архитектурный объект. Пример для ссылки на документ:
```html
[Swagger контракт](/docs/dochub.example.swgr)
```

Будет сформирована ссылка:

[Swagger контракт](/docs/dochub.example.swgr)

Как добавить ссылку на карточку компонента:
```html
[Компонент gitlab](/architect/components/dochub.gitlab)
```

Будет сформирована ссылка:

[Компонент gitlab](/architect/components/dochub.gitlab)

## Встраиваемые объекты DocHub
Интегрирует в документ архитектурный объект. Например:
```Markdown
![Swagger контракт](@document/dochub.example.swgr)
```

Пример встраивания карточки компонента 

![Карточка компонента](@component/dochub.front)


Пример встраивания объекта кастомной сущности ["Взаимодействия"](/entities/interactions/tree)

![Объект кастомной сущности](@entity/interactions/blank?id=dochub.user.check)

Доступны следующие типы объектов: 
* @document
* @context
* @aspect
* @component
* @radar
* @entity

Через "/" указывается идентификатор объекта. 

[Далее](/docs/dochub.tables)
