# Таблицы (Table)

Документ предназначен для отображеия данных в табличной форме.

## Предопределенный набор данных
```yaml
docs:
  dochub.table.preset: 
    type: table               # Тип документа 
    headers:                  # Заголовки таблицы
      - value: id             # Идентификатор поля для вывода в колонке
        text: Идентификатор   # Заголовок колокнки
        sortable: true        # Производить сортировку по колонке
        align: left           # Форматирование по горизонтали
        width: 20%            # Ширина колонки 
      - value: payload
        text: Значение
        sortable: true
        align: left
    source:                   # Данные для таблицы (массив)
      - id: 1                 # Поде "id" 
        payload: Значение 1   # Значение поля "id"
      - id: 2
        payload: Значение 2
```

Результат:
![Предопределенная таблица](@document/dochub.table.preset)

## Запрос к данным архитектуры

Есть возможность выполнять запросы к данным архитектуры. Например, получить список всех интеграций:

```yaml
docs:
  dochub.table.select: 
    type: table
    headers:
      - value: from
        text: Система 1
        sortable: true
        align: left
        width: 20%
        link: link-from       # Идентификатор поля, в котором хранится ссылка
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
    source: >                # JSONata запрос к архитектуре
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
```

Результат:
![Запрос к данным архитектуры](@document/dochub.table.select)

## Хранение запросов в отдельном файле

Располагать запрос непосредственно в манифесте не всегде хорошее решение. Манифест целиком загружается в память. 
Тыжелый запрос создаст излишнюю нагрузку на ОЗУ. Старайтесь тяжелые запросы выделять в отдельные файлы. В этом случае
запрос будет загружаться только при необходимости.

```yaml
docs:
  dochub.table.ex_jsonata:
    type: table               
    headers:                  
      - value: id             
        text: Идентификатор   
        sortable: true        
        align: left           
        width: 20%            
        link: link
      - value: title
        text: Наименование
        sortable: true
        align: left
        link: link
    source: contexts.jsonata   # Файл с запросом JSONata
```

Содержимое файла "contexts.jsonata":

```jsonata
  /* Отбираем все контексты */
  [contexts.$spread().(
      {
          "id": $keys()[0],
          "title": *.title,
          "link": "/architect/contexts/" & $keys()[0]
      }
  )]^(title)
```

Результат:

![Предопределенная таблица](@document/dochub.table.ex_jsonata)

## Хранение данных в отдельном файле

Вы также можете размещать тяжелые данные в отдельном файле YAML или JSON.

```yaml
docs:
  dochub.table.ex_yaml:
    type: table               
    headers:                  
      - value: name             
        text: ФИО автора
        sortable: true        
        align: left           
        width: 100%            
    source: autors.yaml        # Файл с данными
```

Содержимое файла "autors.yaml"

```yaml
- name: Иванов Иван Иванович
- name: Петров Петр Сергеевич
- name: Сидоров Евгений Вальдемарович
```

Результат:

![Предопределенная таблица](@document/dochub.table.ex_yaml)


## Внешние данные

В некоторых случаях полезно иметь возможность получать внешние данные и сопоставлять их с данными архитектуры.
Вы можете сделать это указав в качестве источника данных URI.

```yaml
docs:
  dochub.table.ex_http:
    type: table               
    headers:                  
      - value: title
        text: Name
        sortable: true        
        align: left           
    origin: https://labs.ft.com/tech-radar/demo.json   # Внешний ресурс
    source: showcases.jsonata     # Обработа данных внешнего ресурса
```

Содержимое файла "showcases.jsonata"

```jsonata
[
    $.{
        "title": name
    }
]^(title)
```

Результат:

![Предопределенная таблица](@document/dochub.table.ex_http)


## Генерация таблицы на основании источника данных

Табличные документы могут ссылаться на [источники данных](/docs/dochub.datasets).

```yaml
docs:
  dochub.table.dataset: 
    type: table
    headers:
      - value: location
        text: Документ
        sortable: true
        align: left
        width: 100%
        link: link
    source: dochub.docs         # Идентификтор источника данных
datasets:                       # Источники данных
  dochub.docs:                  # Возвращает список всех документов
    source: >                   # JSONata запрос к архитектуре
      (
        [docs.$spread().{
          "location": *.location,
          "link": "/docs/" & $keys()[0]
        }[location]^(location)]
      )
```

Результат:
![Предопределенная таблица](@document/dochub.table.dataset)

Источники позволяют многократно переиспользовать себя. Это дает возможность не дублировать запросы в разных документах.

## Обработка данных

Перед визуализацией таблицы есть возможность подвергнуть данные обработке.
Для примера, возьмем за основу источник данных "dochub.docs". Его необходимо указать
в поле "origin". В поле "source" нужно определить JSONata запрос. В примере отбираются документы 
располагающиеся в меню "DocHub/Руководство/Документы/".

```yaml
docs:
  dochub.table.dataset.post:    # Табличный с портобработкой данных источника
    type: table
    headers:
      - value: location
        text: Документ
        sortable: true
        align: left
        width: 100%
        link: link
    origin: dochub.docs         # Оригинальный источник данных
    source: >                   # Отбирает документы концепции
      (
        $[$substring(location, 0, 29) = "DocHub/Руководство/Документы/"].{
          "title": $substring(location, 29),
          "link": link
        }
      )
datasets:                       # Источники данных
  dochub.docs:                  # Возвращает список всех документов
    source: >                   # JSONata запрос к архитектуре
      (
        [docs.$spread().{
          "location": *.location,
          "link": "/docs/" & $keys()[0]
        }[location]^(location)]
      )
```


Результат:
![Предопределенная таблица](@document/dochub.table.dataset.post)

[Далее](/docs/dochub.datasets) 