# Источники данных (datasets)

Источники данных во многом работают идентично [таблицам](/docs/dochub.tables) с данными. 
Ключевое их отличие в том, что они сами не визуализуруют их, а предоставляют потребителям.

## Предопределенные данные в источниках

Источники могут содержать предопределенные данные описанные в формате YAML или JSON.

```yaml
datasets:                       
  dochub.components.criticality:  # Определяет уровни критичности
    source:                       # Предопределенные данные
      - id: k1
        title: Уровень K1
      - id: k2
        title: Уровень K2
      - id: k3
        title: Уровень K3
      - id: k4
        title: Уровень K4
```

Результат в виде [таблицы](/docs/dochub.tables):

![Предопределенные данные в источниках](@document/dochub.dataset.preset)

![](@anchor/query-to-data-arch)
## Запросы к данным архитектуры

Есть возможность делать запросы к данным архитектуры используя JSONata запросы.

Например, данный запрос находит все архитектурные компоненты принадлежащие DocHub и сортирует их по названию.

```yaml
datasets:                       # Источники данных
  dochub.components:            # Идентификатор источника
    source: >                   # JSONata запрос к архитектуре
      (
        [components.$spread().{
            "id": $keys()[0],
            "location": *.title,
            "link": "/architect/components/" & $keys()[0]
        }[$substring(id, 0, 7)="dochub."]^(title)]
      )
```

Результат:

![Таблица на основании источника данных](@document/dochub.table.dataset)

## Зависимые источники

Источники данных могут зависить друг от друга. В примере данные получаются из источника "dochub.components" 
и дополнительно обрабатываются.

```yaml
datasets:                       
  dochub.components:            # Возвращает все архитектурные компоненты DocHub
    source: >                   # JSONata запрос к архитектуре
      (
        [components.$spread().{
            "id": $keys()[0],
            "location": *.title,
            "link": "/architect/components/" & $keys()[0]
        }[$substring(id, 0, 7)="dochub."]^(title)]
      )
  dochub.components.l1:         # Выбирает только L1 для архитектурных компонентов DocHub
    origin: dochub.components   # Базовый источник данных
    source: >                   # JSONata запрос к архитектуре
      ($[$count($split(id, "."))=2])
```

Результат:

![Зависимый источник](@document/dochub.dataset.li)

[Далее](/docs/dochub.jsonata)