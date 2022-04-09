# Наследование объектов

Функционал наследования полезен, когда необходимо частично или полностью переиспользовать 
описание объекта. Например, можно создать описание шаблона таблицы и использовать его
наполняя необходимыми данными. 

```yaml
dataset:
  dochub.integrations:          # Базовый источник данных интеграций
    source: >                   # JSONata запрос к архитектуре
      (
        $MANIFEST := $;
        $distinct([components.$spread().(
            $COMPONENT := $;
            $COMPONENT_ID := $keys()[0];
            $.*.links.{
                "from": $COMPONENT.*.title,
                "link-from": "/architect/components/" & $COMPONENT_ID,
                "to": id ? $lookup($MANIFEST.components, id).title : id,
                "link-to": "/architect/components/" & id,
                "contract": contract ? $lookup($MANIFEST.docs, contract).location : contract,
                "link-contract": "/docs/" & contract,
                "title": title,
                "direction": direction
            };
        )])^(from)
      )
docs:
  dochub.table.integrations:          # Базовое описание таблицы интеграций
    type: table
    headers:
      - value: from
        text: Система 1
        sortable: true
        align: left
        width: 20%
        link: link-from       
      - value: to
        text: Система 2
        sortable: true
        align: left
        width: 20%
        link: link-to
      - value: direction
        text: Связь
        sortable: true
        align: center
        width: 5%
      - value: title
        text: Описание
        sortable: true
        align: left
      - value: contract
        text: Контракт
        sortable: true
        align: left
        link: link-contract
  dochub.table.integrations.vuex:     # Дочерняя таблица
    $prototype: dochub.table.select   # Родительская таблица
    origin: dochub.integrations       # Базовый источник данных
    source: >                         # Отбирает только компоненты VUEX
        (
            $[from="VUEX" or to="VUEX"]
        )
```

Результат:
![Унаследованная таблица](@document/dochub.table.integrations.vuex)

Наследование доступно для однотипных объектов:
* [Документов](/docs/dochub.docs);
* [Компонентов](/docs/dochub.components);
* [Контекстов](/docs/dochub.contexts);
* [Источников данных](/docs/dochub.datasets);
* [Аспектов](/docs/dochub.aspects).



