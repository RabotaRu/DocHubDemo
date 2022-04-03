# Компоненты (components)
Компоненты являются базовыми сущностями DocHub. На их основе автоматически генерируются диаграммы связей.
Для успешной генерации компоненты содержат необходимые метаданные.

Пример описания компонента в манифесте [/manifest/components/dochub/l1/front.yaml](/manifest/components/dochub/l1/front.yaml):
```yaml
components:
  #***********************************************************
  #                       C4Model L1
  #***********************************************************
  dochub.front:             # Идентификатор компонента
    title: DocHub           # Название компонента
    entity: component       # Сущность компонента из PlantUML (https://plantuml.com/ru/deployment-diagram)
    technologies:           # Используемые технологии
      - JavaScript
      - VUEJS2
      - Chrome
      - Firefox
      - Safari
    aspects:                # Аспекты, которе реализует компонент
      - dochub.gitlab.auth
      - dochub.manifest.parsing
      - dochub.contexts
      - dochub.aspects
      - dochub.docs
      - dochub.radar
      - dochub.navigation
      - dochub.dataset
    links:                            # Зависимость компонента от других компонентов
      - id: dochub.gitlab             # Идентификатор компонента
        direction: '<--'              # Напрвлене связи
        title: Манифесты и документы  # Надпись на связи
        contract: dochub.swagger      # Идентификатор документа описывающего контракт (может быть прямой ссылкой, например: http://foo.com)
      - id: dochub.plantuml
        direction: '-->'
        title: PlantUML
      - id: dochub.plantuml
        direction: '<-'
        title: Схема SVG
      - id: dochub.web
        direction: '<--'
        title: Манифесты и документы
```

Компоненты обладают карточками, где содержится подробная информация. 
Состав информации может настраиваться через [формы](/docs/dochub.forms). 
Пример карточки для компонента dochub.front:

![Карточка компонента](@component/dochub.front)

[Далее](/docs/dochub.contexts)
