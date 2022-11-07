# Сущности(entities)

Метамодель DocHub можно расширять. Это позволяют делать кастомные сущности.

Пример описания сущности [взаимодействия](/entities/interactions/tree) ([полный исходный файл](/documentation/entities/interactions/entity.yaml)):
```yaml
entities: # Сущности расширенной матемодели
    # Взаимводействия
    interactions:               # Секция, где будет описываться объекты сущности "взаимодействие". Обязательно.
        # Название сущности. Обязательно.
        title: Взаимодействия   
        # Описание сущности текст или ссылка на документ. Необязательно.
        description: >          
            Взаимодействия между компонентами
        # Генератор раcположения в меню. Запрос или явное описание объета. Необязательно.
        menu: >                 
          (
            ... JSONata запрос ...
          )
        # Представления объектов сущности. Обязательно.
        presentations:          
            # Идентификатор представления. Дерево взаимодействий.
            tree:               
                # Тип представления. Обязательно.
                type: plantuml  
                # Путь к шаблону. Обязательно.
                template: templates/tree.puml   
                # Источник данных для рендера шаблона. Возвращает дерево объектов "interactions". Обязательно.
                source: >       
                  (
                      ... JSONata запрос или источник данных ...
                  )  
            blank:              
                type: plantuml  
                template: templates/blank.puml
                source: >
                    (
                        ... JSONata запрос или источник данных ...
                    )
        # JSON Schema контролирующая описание объекта сущности
        schema:                  
            type: object
            patternProperties:
                "[a-zA-Z0-9_]*(\\.[a-zA-Z0-9_]*)*$": # Шаблон ключа
                    type: array
                    minItems: 1
                    items:
                    ...
```

После создания манифеста сущности, можно приступать к описанию манифестов ее объектов.

```yaml
# Использование сущности расширенной метамодели
interactions:
  dochub:
    title: DocHub 
  dochub.user:
    title: Взаимодействия с пользователем
    triggers:
      - Просмотр архитектуры
    steps:
      - from: User
        to: DocHub
        value: Вход на портал
      - from: DocHub
        to: User
        value: Представление архитектурных артефактов
    results:
      - Информация о существующей архитектуре
  dochub.user.check:
    title: Прверка отклонений выявленных валидаторами
    triggers:
      - Просмотр архитектуры
    steps:
      - from: User
        to: DocHub
        value: Выбор раздела Проблемы
      - from: DocHub
        to: User
        value: Генерация дерева со статусами
      - from: User
        to: DocHub
        value: Выбор отклонения
      - from: DocHub
        to: User
        value: Карточка отклонения
    results:
      - Информация об отклонениях
  dochub.user.research:
    title: Изучение документации
    triggers:
      - Просмотр документов
    steps:
      - from: User
        to: DocHub
        value: Выбор раздела Документы
      - from: DocHub
        to: User
        value: Представление
    results:
      - Актуальная информация из документов    
```

Пример представления сущности можно посмотреть [здесь](/entities/interactions/blank?id=dochub.user).

[Далее](/docs/dochub.radar)
