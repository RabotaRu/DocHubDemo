# Шаблоны

Шаблоны предназначены для генерации документов на основании результатов [JSONata](https://jsonata.org/) запросов
с использованием [mustache](https://mustache.github.io/) языка.

## Markdown

Предоставляется возможность динамически создавать документы:
```yaml
docs:
  ...
  dochub.templates:         # Пример генерации документа по шаблону
    location: DocHub/Руководство/Документы/Шаблоны
    description: Markdown
    type: markdown
    autor: R.Piontik        # Кастомное поле - Автор документа
    approvers:              # Кастомное поле - список согласующих
      - P.Petrov
      - S.Sidorov
      - N.Nikolaev
    source: >               # JSONata запрос для формирования параметров шаблона
      (
        {
          "id": $self._id,              /* Идентификатор документа */
          "autor": $self.autor,         /* Автор документа */
          "approvers": $self.approvers, /* Согласующие */
          "docs": [docs.$spread().{     /* Другие документы автора */
            "id": $keys()[0],
            "title": *.description,
            "autor": *.autor
          }][autor=$self.autor]
        }
      )
    subjects:
      - dochub.front
      - dochub.front.spa
      - dochub.front.spa.blank
      - dochub.front.spa.blank.doc
    template: templates.md  # Шаблон документа
  ...
```

Код шаблона:
```mustache
{{=<% %>=}}
Результат:
* Идентификатор документа: **{{id}}**
* Автор: **{{autor}}**
* Согласующие: {{#approvers}}**{{.}}**; {{/approvers}}
* Другие документы автора:
{{#docs}}
  * [{{title}}](/docs/{{id}})
{{/docs}}
<%={{ }}=%>
```

Результат:
* Идентификатор документа: **{{id}}**
* Автор: **{{autor}}**
* Согласующие: {{#approvers}}**{{.}}**; {{/approvers}}
* Другие документы автора:
{{#docs}}
  * [{{title}}](/docs/{{id}})
{{/docs}}

## PlantUML

Доступна генерация PlantUML документов:
```yaml
docs:
  ...
  dochub.templates.pml: # Пример генерации PlantUML документа по шаблону
    type: PlantUML
    source: >
      (
        {
          "entities": $distinct([     /* Получаем все использованные сущности при описании архитектуры */ 
              components.*.entity
          ])
        }
      )
    subjects:
      - dochub.front
      - dochub.front.spa
      - dochub.front.spa.blank
      - dochub.front.spa.blank.doc
    template: examples/template.puml
  ...
```

Код шаблона:
```mustache
{{=<% %>=}}
@startuml
title Сущности использованные при описании архитектуры
{{#entities}}
{{{.}}} {{{.}}} as {{{.}}}
{{/entities}}
@enduml
<%={{ }}=%>
```

Результат:
![PlantUML по шаблону](@document/dochub.templates.pml)

## AsyncAPI

Важным приемуществом шаблонов, является возможность консолидации фрагментированных
артефактов в один. Например, можно в каждом отдельном компоненте описать контракты, а затем 
собрать общий.

Манифест документа:
```yaml
docs:
  ...
  dochub.templates.asyncapi: # Пример генерации AsyncAPI документа по шаблону
    type: AsyncAPI
    source: >
      (
        $BODY := $mergedeep([components.*.asyncapi]);
        {
            "content": [$BODY.$spread().{
                "field": $keys()[0],
                "body": $string($lookup($, $keys()[0]))
            }]
        }
      )
    template: examples/asyncapi_template.json
  ...
```

Шаблон:
```mustache
{{=<% %>=}}
{
  "asyncapi": "2.4.0",
  "info": {
    "title": "Пример генерации асинхронных контрактов",
    "version": "1.0.0",
    "description": "Информация о контрактах собирается по всей архитектуре"
  }
  {{#content}}
    ,"{{field}}":{{&body}}
  {{/content}}
}
<%={{ }}=%>
```


Манифест компонентов:
```yaml
components:
  ...
  #***********************************************************
  #               Компонет-пример сервиса заказов
  #***********************************************************
  dochub.examples.orders:
    title: Сервис управления заказами
    entity: component
    technologies:
      - PHP
    asyncapi:     # Кастомное поле, созданное для примера сборки контрактов из архитектуры через шаблоны
      servers:
        orders:
          url: mqtt://order.host.net
          protocol: mqtt
          description: Orders gateway
      channels:
        order/create:
          subscribe:
            operationId: emitOrderCreate
            message:
              $ref: "#/components/messages/OrderCreate"
      components:
        messages:
          OrderCreate:
            name: orderCreate
            title: Создание заказа
            contentType: application/json
            payload:
              $ref: "#/components/schemas/order"
        schemas:
          order:
            type: object
            properties:
              id:
                type: string
                format: uuid
              customer:
                type: string
                format: uuid
              curr:
                type: string
                description: "Валюта"
              value:
                type: number
                description: "Сумма"
              createdAt:
                type: string
                format: date-time
                description: "Момент создания"
  #***********************************************************
  #               Компонет-пример сервиса оплаты
  #***********************************************************
  dochub.examples.payment:
    title: Сервис оплаты   
    entity: component
    expert: R.Piontik
    technologies:
      - SberPay
      - Go
    asyncapi:     # Кастомное поле, созданное для примера сборки контрактов из архитектуры через шаблоны
      servers:
        payments:
          url: mqtt://pay.host.net
          protocol: mqtt
          description: Payment gateway
      channels:
        pay/payment:
          subscribe:
            operationId: emitPayment
            message:
              $ref: "#/components/messages/Payment"
      components:
        messages:
          Payment:
            name: payment
            title: Оплата
            summary: Сообщение по оплате
            contentType: application/json
            payload:
              $ref: "#/components/schemas/payment"
        schemas:
          payment:
            type: object
            properties:
              account:
                type: string
                description: "Номер счета"
              curr:
                type: string
                description: "Валюта"
              value:
                type: number
                description: "Сумма"
              createdAt:
                type: string
                format: date-time
                description: "Момент создания"
  ...
```

Результат:
![AsyncAPI по шаблону](@document/dochub.templates.asyncapi)


## OpenAPI

Аналогично AsyncAPI можно собрать единый контракт для OpenAPI.

Манифест документа:
```yaml
docs:
  ...
  dochub.templates.openapi: # Пример генерации OpenAPI документа по шаблону
    type: OpenAPI
    source: >
      (
        $BODY := $mergedeep([components.*.openapi]);
        {
            "content": [$BODY.$spread().{
                "field": $keys()[0],
                "body": $string($lookup($, $keys()[0]))
            }]
        }
      )
    template: examples/openapi_template.json
  ...
```

Шаблон:
```mustache
{{=<% %>=}}
{
  "openapi": "3.0.0",
  "info": {
    "title": "Пример шаблона OpenAPI",
    "description": "Контракты собираются из нескольких компонентов архитетуры"
  },
  "servers": [
    {
      "url": "http://host.net",
      "description": "Сервис заказов"
    }
  ]
  {{#content}}
    ,"{{field}}":{{&body}}
  {{/content}}
}
<%={{ }}=%>
```

Манифест компонентов:
```yaml
components:
  ..
  #***********************************************************
  #               Компонет-пример сервиса заказов
  #***********************************************************
  dochub.examples.orders:
    title: Сервис управления заказами
    entity: component
    technologies:
      - PHP
    ...
    openapi: # Кастомное поле, созданное для примера сборки контрактов из архитектуры через шаблоны
      paths:
        /orders:
          get:
            summary: Получение списка заказов
            responses:
              '200':    # status code
                content:
                  application/json:
                    schema:
                      type: array
                      items:
                        type: string
                        format: uid
  #***********************************************************
  #               Компонет-пример сервиса оплаты
  #***********************************************************
  dochub.examples.payment:
    title: Сервис оплаты   
    entity: component
    expert: R.Piontik
    technologies:
      - SberPay
      - Go
    ...
    openapi: # Кастомное поле, созданное для примера сборки контрактов из архитектуры через шаблоны
      paths:
        /payments:
          get:
            summary: Получение списка счетов
            responses:
              '200':    # status code
                content:
                  application/json:
                    schema:
                      type: array
                      items:
                        type: string
                        format: uid
    ...
```

Результат:
![OpenAPI по шаблону](@document/dochub.templates.openapi)


[Далее](/docs/dochub.datasets) 