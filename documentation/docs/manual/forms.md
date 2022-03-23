# Формы (forms)

Любой архитектурный объект можно обогатить необходимыми вам метаданными. В этом вам помогут формы.

Пример описания формы:
```yaml
forms:                    # Формы
  - entity:               # Сущности для которых форма релевантна (см. PlantUML)
      - component         # В данном случае, это компонент
    fields:               # Поля формы
      source_file:        # Идентификатор поля
        title: Файл       # Название поля
        required: true    # Признак обязательности 
```

В манифесте компонента заполняем поле "source_file":
```yaml
title: Рабочая\nобласть
entity: component
source_file: src/components/Root.vue
technologies:
  - JavaScript
  - VUE2
links:
  - id: dochub.router
    title: Представление
    direction: -->
  - id: dochub.router
    title: Параметры
    direction: <--
```

Теперь, в карточке компонента будут выводиться информация о файле реализации компонента:

![Карточка компонента](@component/dochub.front.spa.workspace) 


[Далее](/docs/dochub.technologies)
