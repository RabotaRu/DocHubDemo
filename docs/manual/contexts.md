# Контексты представлений (contexts)
Имеет смысл отображать [компоненты](/docs/dochub_components) архитектуры в контексте их рассмотрения. Например, если рассматривается
архитектура приложения, нет смысла отображать компоненты сетевой архитектуры на той же схеме.
Разумно разделить эти представления. Контексты позволяют решить эту задачу.

Можно сказать, что контексты реализуют идею [C4 Model](https://ru.wikipedia.org/wiki/%D0%9C%D0%BE%D0%B4%D0%B5%D0%BB%D1%8C_C4).
Отличие состоит в том, что контексты не ограничивают вас в количестве уровней представления.

Структура манифеста контекста:
```yaml
contexts:                                   # Контексты представления архитектурных компонентов
    dochub.c4level1:                        # Идентификатор контекста
        title: Общая архитектура DocHub     # Название контекста. Отображается в заголовке диаграммы
        location: DocHub/Общая архитектура  # Размещение документа в дереве навигации
        extra-links: false                  # Указывает, что не нужно отображать компоненты явно не указанные в контексте
        components:                         # Компоненты входящие в контекст
            - dochub.front                  # Идентификатор компонента
        uml:                                # PlantUML инъекции
          $before: >                        # Вставляет код ПЕРЕД сгенерированным телом диаграммы
            ...                             
          $after: >                         # Вставляет код ПОСЛЕ сгенерированным телом диаграммы
            ...
        uml: ../docs/sequence.puml          # Ссылка на файл с PlantUML кодом полностью переопределяет представление
```
Чтобы понять это лучше, рассмотрим DocHub в C4 Model:

![](@anchor/c4model_1)

## 1. Уровень I: Система в масштабе ее взаимодействия с пользователями и другими системами

![Система в масштабе ее взаимодействия с пользователями и другими системами](@context/dochub.c4level1)

Описание контекста в манифесте [/manifest/contexts.yaml](/manifest/contexts.yaml):
```yaml
contexts:   # Контексты представления архитектурных компонентов
    dochub.c4level1:
        title: Общая архитектура DocHub     # Название контекста
        location: DocHub/Общая архитектура  # Размещение документа в дереве навигации
        components:                         # Компоненты входящие в контекст
            - dochub.front
            - dochub.user
            - plantuml.server
            - gitlab.gitlab
            - web.web
    ...
```
Это простейшее описание контекста. По сути, контекст описывает какие компоненты будут отображаться на схеме,
под каким названием и где пользователь сможет найти контекст в навигационном меню. Связи между компонентами описаны
в самих компонентах (см. [компоненты](/docs/dochub_components)).

![](@anchor/c4model_2)

## 2. Уровень II: Взаимосвязанные контейнеры
![Взаимосвязанные контейнеры](@context/dochub.c4level2)

Описание контекста в манифесте [/manifest/contexts.yaml](/manifest/contexts.yaml):
```yaml
contexts:   # Контексты представления архитектурных компонентов
    ...
    dochub.c4level2:
        title: Контейнерная архитектура DocHub
        location: DocHub/Контейнерная архитектура
        components:
            - dochub.browser
            - dochub.browser_localstorage
            - dochub.spa
            - gitlab.api
            - gitlab.oauth
            - gitlab.repository
            - gitlab.gitclient
            - plantuml.server
            - web.web
    ...
```
![](@anchor/c4model_3)

## 3. Уровень III: Взаимосвязанные компоненты
![Взаимосвязанные компоненты](@context/dochub.c4level3)

Описание контекста в манифесте [/manifest/contexts.yaml](/manifest/contexts.yaml):
```yaml
contexts:   # Контексты представления архитектурных компонентов
    ...
    dochub.c4level3:
        title: Компонентная архитектура DocHub
        location: DocHub/Компонентная архитектура
        uml: # Позволяет внедрить исходный код PlantUML
            $before: > # В начало схемы
              skinparam {
                  'linetype ortho
                  nodesep  50
                  ranksep  50
              }
        components:
            - dochub.blank_aspect
            - dochub.blank_context
            - dochub.markdown
            - dochub.vue2
            - dochub.router
            - dochub.vuex
            - dochub.menu
            - dochub.workspace
            - dochub.jsonata
            - dochub.manifest_parser
            - dochub.swagger
            - dochub.blank_component
            - dochub.blank_doc
            - dochub.oauth
            - gitlab.oauth
            - gitlab.api
            - gitlab.repository
            - web.web
    ...
```

Наглядно видно, что визуализация третьего уровня перегружена. Для оптимизации представления схемы используется
донастройка представления PlantUML через [skinparam](https://plantuml.com/ru/skinparam).

Контексты позволяют вставлять PlantUML код в начало и вконец схемы. А также полностью заменять автогенерацию схемы
PlantUML кодом. Пример будет рассмотрен на [уровне IV](#c4model_4).

Такая ситуация может возникнуть
на любом уровне. Это зависит от масштаба архитектуры и детальности ее описания.

Контексты позволяют выделить секции архитектурных объектов на каждом уровне и рассмотреть их отдельно:

![](@anchor/c4model_3_1)

### 3.1. Консолидация манифестов:
![Консолидация манифестов](@context/dochub.manifest_build)

Описание контекста в манифесте [/manifest/contexts.yaml](/manifest/contexts.yaml):
```yaml
contexts:   # Контексты представления архитектурных компонентов
    ...
    dochub.manifest_build:
        title: Консолидация манифестов
        location: DocHub/Компонентная архитектура/Консолидация манифестов
        components:
            - dochub.vuex
            - dochub.manifest_parser
            - gitlab.api
            - gitlab.repository
    ...
```

![](@anchor/c4model_3_2)

### 3.2. Парсинг и представление манифестов:

![Парсинг и представление манифестов](@context/dochub.manifest_presentation)

Описание контекста в манифесте [/manifest/contexts.yaml](/manifest/contexts.yaml):
```yaml
contexts:   # Контексты представления архитектурных компонентов
    ...
    dochub.manifest_presentation:
        title: Парсинг манифестов
        location: DocHub/Компонентная архитектура/Парсинг манифестов
        extra-links: false # Указывает, что не нужно отображать компоненты явно не указанные в контексте
        uml:
            $before: >
              skinparam {
                nodesep  100
                ranksep  100
            }
        components:
            - dochub.vuex
            - dochub.jsonata
            - dochub.blank_aspect
            - dochub.blank_context
            - dochub.menu
    ...
```

Обратите внимание, в манифесте данного контекста встречается директива "extra-links". Она управляет
визуализаций связанных компонентов. По умолчанию, в контексте отображаются перечисленные компоненты, а также
связанные с ними ([links](#links) в компонентах). В большинстве случаев такое поведение необходимо для более
наглядного отображения контекста. Но в некоторых случаях, является излишним.

![](@anchor/c4model_3_3)

### 3.3. Представление документов
![Представление документов](@context/dochub.render_doc)

Описание контекста в манифесте [/manifest/contexts.yaml](/manifest/contexts.yaml):
```yaml
contexts:   # Контексты представления архитектурных компонентов
    ...
    dochub.render_doc:
        title: Представление документов
        location: DocHub/Компонентная архитектура/Представление документов
        extra-links: false
        components:
            - dochub.blank_doc
            - dochub.swagger
            - plantuml.server
            - dochub.markdown
            - gitlab.api
            - gitlab.repository
    ...
```
![](@anchor/c4model_4)

## 4. Уровень IV: дополнительные сведения о дизайне архитектурных элементов
![дополнительные сведения о дизайне архитектурных элементов](@context/dochub.sequence)

Описание контекста в манифесте [/manifest/contexts.yaml](/manifest/contexts.yaml):
```yaml
contexts:   # Контексты представления архитектурных компонентов
    ...
    dochub.sequence:
        title: Диаграмма взаимодействия DocHub
        location: DocHub/Диаграмма взаимодействия
        uml: ../docs/sequence.puml # Ссылка на файл с кодом PlantUML
    ...
```
Стандартным методом представления контекстов является автоматическая генерация PlantUML кода на основании манифеста
контекста, где перечисленны компоненты. Компоненты содержат достаточную информацию для построения диаграммы связей.
Но в некоторых случаях, нет необходимости в таком подходе. Например, если необходимо описать диаграмму взаимодействия
(как здесь). Другим примером может стать концептуальное проектирование. В этом случае до утверждения схемы,
нет необходимости генерировать манифесты компонентов.

Контексты позволяют переопределить представление на PlantUML код. В этом случае в поле "uml" указывается ссылка на
файл, содержащий код.

[Далее](/docs/dochub_aspects)
