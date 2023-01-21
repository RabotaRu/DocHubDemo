# Network

Сетевые диаграммы позволяют наглядно представлять топологию различных связей на основании данных архитектуры.
Для визуализации сетей используется библиотека [vis.js](https://visjs.org/).

Данными для пострения сети являются константные данные в поле source, [источник данных](/docs/dochub.datasets) или результат запроса JSONata.

Итоговым результатом должна стать структура в соответсвии с [документацией vis.js](https://visjs.github.io/vis-network/docs/network/) :
```json
    {
        "nodes": [ /* Перечисление нод https://visjs.github.io/vis-network/docs/network/nodes.html */
            {
               "id": "node1",           /* Идентификатор ноды */
               "label": "Node 1",       /* Описание */
               ...
            },
            {
               "id": "node2",           
               "label": "Node 2",
               ...
            }
        ],
        "edges": [                       /* Перечисление вязей нод https://visjs.github.io/vis-network/docs/network/edges.html */
            {
                "id": "link1",           /* Идентификатор связи */
                "from": "node1",         /* Источник */
                "to": "node2",           /* Приемник */
                "label": "Пример связи", /* Описание */
                ...
            }
        ],
        "options": {                     /* Параметры рендеринга диаграммы https://visjs.github.io/vis-network/docs/network/ */
            "clickToUse": false,         /* Отключаем обязательный клик перед взаимодействем с пользователем */
            ...
        }
    }
```

Пример описания сетевой диаграммы:

```yaml
  ...
  dochub.example.network:
    type: Network
    subjects:
      - dochub.front
      - dochub.front.spa
      - dochub.front.spa.blank
      - dochub.front.spa.blank.doc
    source: >
      (
        $manifest := $;
        /* Перебираем все компоненты */
        $adges  := $distinct(components.$spread().(
          $node_id := $keys()[0];
          $node := $.*;
          /* Определяем связи */
          $node.links.{
            "from": $node_id,
            "to": id,
            "label": title
          }
        ));
        /* Выявляем ноды из связей */
        $nodes := $distinct($append($adges.from, $adges.to)).(
          $struct := $split($, ".");
          $group := $replace($, "." & $reverse($struct)[0], "");
          {
            "id": $,
            "label": $lookup($manifest.components, $).title,
            "group": $group,
            "level": $count($struct),
            /* Позиционируем ноду тем выше, чем выше слой */
            "y": $count($struct) * 200
          }
        );
        /* Добавляем ноды легенды */
        $nodes := $append($nodes, (
          /* Получаем максимальную точку позиции нод */
          $height := $max($nodes.y);
          $groups := $distinct($nodes.group)^($);
          /* Вычисляем центр легенды */
          $offset := -($height / 2);
          /* Определяем шаг легенды */
          $step := $height / $count($groups);
          /* Строим легенду */
          $map($groups, function($v, $i, $a) {
            {
              "id": "legend-" & $string($i),
              "x": -600,
              "y": $i * $step + $offset,
              "shape": "square",
              "label": $v,
              "group": $v,
              "value": 1,
              "fixed": true,
              "physics": false
            }
          })
        ));
        /* Выводим результат */
        {
          "nodes": $nodes, /* Массив нод */
          "edges": $adges, /* Массив связей */
          "options": {     /* Параметры сетевой диаграммы https://visjs.github.io/vis-network/docs/network/ */
            "clickToUse": false /* Отключаем обязательный клик перед взаимодействем с пользователем */
          }
        }
      )
  ...
```


Пример рендеринга документа:

![Документ](@document/dochub.example.network)

[Далее](/docs/dochub.swagger)