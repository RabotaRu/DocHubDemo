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

## Зависимость

Источники данных могут зависеть друг от друга. В примере данные получаются из источника "dochub.components" 
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

## Множественные зависимости

Иногда возникает необходимость консолидировать данные из нескольких источников для последующей обработки.
Это возможно сделать через структуру в origin.

```yaml
  dochub.multi_dependencies:
    origin:
      integrations: dochub.integrations             # Указываем зависимость от источника "dochub.integrations"
      criticality: dochub.components.criticality    # Указываем зависимость от источника "dochub.components.criticality"
      manifest: "($)"                               # Указывается зависимость от результата запроса JSONata - "($)"
    source: >
      (
        /* Сохраняем контекст для использования в глубине */
        $CONTEXT := $;

        /* Обрабатываем данные подготовленные источником "dochub.integrations" */
        integrations.(
            /* Восстанавливаем ID компонента, чтобы получить по нему данные */
            $ID_FROM := $reverse($split($."link-from", "/"))[0];
            $ID_TO := $reverse($split($."link-to", "/"))[0];

            /* Получаем данные критичности */
            $CRITICALLY_FROM := $lookup($CONTEXT.manifest.components, $ID_FROM).criticality;
            $CRITICALLY_FROM := $CRITICALLY_FROM ? $CRITICALLY_FROM : 4;
            $CRITICALLY_TO := $lookup($CONTEXT.manifest.components,$ID_TO).criticality;
            $CRITICALLY_TO := $CRITICALLY_TO ? $CRITICALLY_TO : 4;
            $CRITICALLY := $CRITICALLY_FROM < $CRITICALLY_TO ? $CRITICALLY_FROM : $CRITICALLY_TO;

            /* Обогащаем данные DataSet "dochub.integrations" информацией о критичности связи */
            $merge([$, {
                "criticality": $CONTEXT.criticality[id="k" & $CRITICALLY].title
            }])
        );
      )
```

Результат:

![Зависимый источник](@document/dochub.table.multi_dependencies)


[Далее](/docs/dochub.jsonata)