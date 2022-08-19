# Технологии (technologies)

Важным процессом в управлении архитектурой является управление технологическим стеком. DocHub позволяет решить
эту задачу через манифесты технологического стека. В манифесте перечисляются технологии и их статус.

```yaml
technologies:                   # Описание технологического стека
    sections:                   # Определение разделов стека
        language:               # Идентификатор раздела
            title: Языки программирования   # Название раздела
        parsers:
            title: Парсеры
        tools:
            title: Инструментарий
        storages:
            title: Хранилища
        browsers:
            title: Браузеры
    items:                      # Перечисление технологий
        JavaScript:             # Идентификатор технологии
            aliases:            # Синонимы технологии
                - js
                - NodeJS
            title: Супер-крутой язык программирования           # Название технологии
            link: https://ru.wikipedia.org/wiki/JavaScript      # Ссылка на документацию
            section: language   # Идентификатор секции технологии
            status: adopt       # Статус технологии adopt / trial / assess / hold
```

На основании анализа архитектурных компонентов происходит визуализация технологического радара. Для этого в 
компонентах указываются используемые им технологии. 

```yaml
title: Рабочая\nобласть
entity: component
source_file: src/components/Root.vue
technologies:                   # Используемые технологии
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

[Посмотреть технологический радар](/techradar)

Описанные технологии также являются архитектурными объектами и имеют карточки. 
Например, карточка для JavaScript:

![Карточка технологии](@technology/JavaScript)

[Далее](/docs/dochub.forms)
