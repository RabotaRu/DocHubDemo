# Компоненты (components)
Компоненты являются базовыми сущностями DocHub. На их основе автоматически генерируются диаграммы взаимодействия.
Для успешной генерации компоненты содержат необходимые метаданные.

Пример описания компонента в манифесте [/manifest/components/dochub/l1/front.yaml](/manifest/components/dochub/l1/front.yaml):
```yaml
title: DocHub         # Название компонента
entity: rectangle     # Сущность компонента из PlantUML (https://plantuml.com/ru/deployment-diagram)
technologies:         # Используемые технологии
  - JavaScript
  - VUEJS2
  - Chrome
  - Firefox
  - Safari
aspects:              # Аспекты, которе реализует компонент
  - gitlab.auth
  - manifest.parsing
  - dochub.contexts
  - dochub.aspects
  - dochub.docs
  - dochub.radar
  - dochub.navigation
links:                # Связь компонента с другими компонентами
  - id: gitlab.gitlab # Идентификатор компонента
    direction: '<--'  # Специфика связи в формате PlantUML (https://plantuml.com/ru/component-diagram)
    title: Манифесты и документы
  - id: plantuml.server
    direction: '-->'
    title: PlantUML
  - id: plantuml.server
    direction: '<-'
    title: Схема SVG
  - id: dochub.user
    direction: '->'
    title: Просмотр
  - id: web.web
    direction: '<--'
    title: Манифесты и документы
```

[Далее](/docs/dochub_contexts)
