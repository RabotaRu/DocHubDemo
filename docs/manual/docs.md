# Документы (docs)

Документ - многофункциональная сущность. Поддерживаются форматы:
* **[PlantUML](#plantuml)** - позволяет создавать диаграммы из обычного текстового языка. [Подробнее](https://plantuml.com/ru/).
* **[Swagger (OpenAPI)](#swagger)**  - язык описания интерфейсов для описания RESTful API, выраженных с помощью JSON. [Подробнее](https://swagger.io/);
* **[Markdown](#markdown)** - облегчённый язык разметки, созданный с целью обозначения форматирования в простом тексте. [Подробнее](https://ru.wikipedia.org/wiki/Markdown)

<br>
Описание контекста в манифесте [/manifest/aspects.yaml](/manifest/aspects.yaml):

```yaml
docs:                                 # Документы
    dochub_manual:                    # Идентификатор документа
        icon: lightbulb_outline       # Иконка, которы будет отображаться в дереве навигации
        location: DocHub/Руководство  # Размещение документа в дереве навигации
        description: Пользовательское руководство DocHub  # Краткое описание сути документа
        type: markdown                # Тип документа (OpenAPI / markdown / PlantUML)
        subjects:                     # К какому архитектурному объекту документ имеет отношение
            - dochub.front            # Идентификатор архитектурного объекта
        source: ../docs/manual.md     # Путь к документу 
```

Документы могут иметь связи с архитектурными объектами. Для этого в секции "subjects" необходимо перечислить их
идентификаторы. Если объект связан с документом, в его карточке он будет отображаться. Например, для объекта
**dochub.front** карточка выглядит так:

<dochub-object type="component" subject="dochub.front" style="border: #ccc 1px solid">
    [ТУТ ДОЛЖЕНА БЫТь КАРТОЧКА КОМПОНЕНТА]
</dochub-object>

В разделе "Документы" можно найти ссылки на связанные с объектом документы.

Для лучшего понимания возможностей документов разберем отдельно каждый тип:

<dochub-anchor id="plantuml"/>

## PlantUML

Представляет собой исходные код на языке PluntUML.

```text
@startuml
    participant Participant as Foo
    actor       Actor       as Foo1
    boundary    Boundary    as Foo2
    control     Control     as Foo3
    entity      Entity      as Foo4
    database    Database    as Foo5
    collections Collections as Foo6
    queue       Queue       as Foo7
    Foo -> Foo1 : To actor
    Foo -> Foo2 : To boundary
    Foo -> Foo3 : To control
    Foo -> Foo4 : To entity
    Foo -> Foo5 : To database
    Foo -> Foo6 : To collections
    Foo -> Foo7 : To queue
@enduml
```

Пример рендеринга документа:

<dochub-object type="document" subject="example_pml" style="border: #ccc 1px solid">
    [ТУТ ДОЛЖЕНА БЫТь КАРТОЧКА КОМПОНЕНТА]
</dochub-object>

<dochub-anchor id="swagger"/>

## Swagger (OpenAPI)

Данный вид документов предназначен для описания контрактов. Рассмотрим пример контракта с официального
[сайта](https://swagger.io/docs/specification/basic-structure/).

```yaml
openapi: 3.0.0
info:
  title: Sample API
  description: Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.
  version: 0.1.9
servers:
  - url: http://api.example.com/v1
    description: Optional server description, e.g. Main (production) server
  - url: http://staging-api.example.com
    description: Optional server description, e.g. Internal staging server for testing
paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in CommonMark or HTML.
      responses:
        '200':    # status code
          description: A JSON array of user names
          content:
            application/json:
              schema: 
                type: array
                items: 
                  type: string
```

Представление документа в DocHub:

<dochub-object type="document" subject="example_swgr" style="border: #ccc 1px solid">
    [ТУТ ДОЛЖЕНА БЫТь SWAGGER КОНТРАКТ]
</dochub-object>


<dochub-anchor id="markdown"/>

## Markdown

В Markdown документах включена поддержка HTML. Добавлены специальные теги DocHub, которые позволяются
заметно расширить функциональность:

### dochub-anchor
Позволяет создавать именованные якоря для "коротких" ссылок внутри документа. Например, код:
```html
<dochub-anchor id="markdown"/>
```
создает якорь. Теперь на него можно сослаться из любой части документа:
```markdown
[Пример короткой ссылки](#markdown)
```

### dochub-object
Интегрирует в документ архитектурный объект. Например, код:
```html
<dochub-object type="document" subject="example_swgr" style="border: #ccc 1px solid">
    [ТУТ ДОЛЖЕНА БЫТь SWAGGER КОНТРАКТ]
</dochub-object>
```
Результат можно посмотреть [выше](#swagger).

В свойстве "type" указывается тиб объекта. Возможны значения:
* document
* context
* aspect
* component

В свойстве "subject" указывается идентификатор объекта. 

[Далее](/docs/dochub_forms)
