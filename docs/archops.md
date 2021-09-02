## Введение
DocHub - инструмент управления архитектурой автоматизированных систем.

Описание архитектуры осуществляется через [манифесты](#terms). Поддерживаются два формата файлов: JSON и YAML. 
Предпочтительным является формат YAML. Этот формат поддерживает комментарии, что существенно упрощает описание 
архитектуры.

Пример законченного манифеста можно посмотреть  [тут](/manifest/dochub.yaml). Он описывает 
архитектуру DocHub. Манифест состоит из разделов:
* **[import](#imports)** - 
                     Позволяет подключать дополнительные манифесты.
* **[namespaces](#namespaces)** - 
                     Определяет пространства имен. Все [архитектурные объекты](#terms) должны иметь уникальные
                     идентификаторы. Пространство имен позволяет избежать конфликта идентификаторов в случае
                     описания нескольких подсистем имеющих схожие архитектурные черты.
* **[contexts](#contexts)** - 
                     Контексты представления [архитектурных компонентов](#terms). Позволяет выделить группу
                     компонентов и отобразить ее по смыслу заложенному в контекст. Например, выделить
                     все компоненты инфраструктуры. Через контексты реализуется смысл
                     [C4 Model](https://ru.wikipedia.org/wiki/%D0%9C%D0%BE%D0%B4%D0%B5%D0%BB%D1%8C_C4).
* **[aspects](#aspects)** - 
                     Архитектурные аспекты. Позволяют выделить и отобразить на схеме компоненты 
                     реализующие, например, бизнес-функцию. 
* **[docs](#docs)** - 
                     Документы необходимые для детального описания архитектуры. Например, контракты в
                     формате OpenAPI, PlantUML диаграммы, markdown документация.
* **[forms](#forms)** -
                     Настраиваемые формы карточек архитектурных объектов.
* **[components](#components)** -
                     Архитектурные компоненты.
* **technologies** - Технологический стек. Позволяет строить технологический радар. 

<br>

<dochub-anchor id="terms"/>

## Термины и определения
<br>

* **Архитектурный компонент** - атомарный элемент представления архитектурны на диаграмме.
* **Архитектурный объект** - выраженный манифестом архитектурный компонент.
* **Манифест** - структурированный файл, в котором описываются архитектурные компоненты и связи между ними.
 
<br>

<dochub-anchor id="imports"/>

## Импорт манифестов (imports)
Существует возможность описать всю архитектуру в одном манифесте. Но работа с единым файлом имеет 
ряд недостатков:
1. В объемном файле сложно контролировать вносимые изменения;
2. Устранение конфликтов усложняется, т.к. над одним файлом могут работать срезу несколько человек;
3. Обозримость описания архитектуры значительно ухудшается.

<br>Рекомендуется использовать структуру файлов и папок для устранения этих недостатков. Например, 
в отдельном файле развивать каждую секцию. Это позволяет сделать секция "import". 
Файловая структура может быть такой:
```text
./components/component1.yaml        - архитектурный компонент 1
./components/component2.yaml        - архитектурный компонент 2
./components/../../componentN.yaml  - архитектурный компонент N
./docs/doc1.yaml                    - документ 1
./docs/doc2.yaml                    - документ 2
./docs/../docN.yaml                 - документ N
./aspects.yaml                      - архитектурные аспекты
./contexts.yaml                     - контексты представления архитектуры
./components.yaml                   - архитектурные компоненты
./docs.yaml                         - документы
./technologies.yaml                 - технологический стек
./root.yaml                         - корневой манифест, содержащий только секцию "import"
./namespaces.yaml                   - пространства имен
```

В этом случае корневой манифест (root.yaml) будет выглядеть так: 

```yaml
imports:
  - aspects.yaml
  - contexts.yaml
  - components.yaml
  - docs.yaml
  - technologies.yaml
  - namespaces.yaml
```

Тогда, для примера, манифест компонентов (components.yaml) может быть таким:
```yaml
components:
  namespace.component1: components/component1.yaml
  namespace.component2: components/component2.yaml
  ...
  namespace.component3:
    title: Services
    entity: component
    technologies:
      - Go
    presentations:
      - contexts:
          - namespace.general
  ...
```
Следует отметить, что DocHub позволяет указывать описание объектов ссылками. В данном примере
для _namespace\.component1_ и _namespace\.component2_ указана ссылка (относительная) на файл описания. 
Компонент _namespace\.component3_ описан непосредственно в файле манифеста. 

Подключение дополнительных манифестов возможно из сторонних репозиториев **того же** инстанса GitLab,
а также с web-ресурсов по http/https протоколу. 

<dochub-anchor id="namespaces"/>

## Области имен (namespaces)

При описании нескольких автоматизированных систем, со схожими архитектурными чертами, может возникнуть
конфликт идентификаторов архитектурных объектов. Для решения этой проблемы в DocHub введены 
пространства имен - namespaces. В структуре идентификатора идентификатор пространства является 
префиксом отделенным от идентификатора объекта символом ".".

Пример:
```text
dochub.front
```

Здесь объект с идентификатором _front_ принадлежит пространству имен _dochub_.

При построении диаграмм, компоненты группируются в области на основании пространства имен. Например:

<dochub-object type="context" subject="dochub.c4level1">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

На схеме отражены компоненты принадлежащие четырем пространствам имен: DocHub; GitLab; plantuml; web.

Если идентификатор принадлежит неописанной области имен, DocHub будет сообщать 
о наличии проблемы "Область имен отсутствует". Описание области имен производится в секции
"namespaces":

```yaml
namespaces:
    - dochub: # Идентификатор области имен
        title: DocHub # Название области имен. Будет отражаться на схемах
    - gitlab:
        title: GitLab
```

В данном примере описаны две области пространства имен: dochub и gitlab.

<dochub-anchor id="components"/>

## Компоненты (components)
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

<dochub-anchor id="contexts"/>

## Контексты представлений (contexts)
Имеет смысл отображать [компоненты](#components) архитектуры в контексте их рассмотрения. Например, если рассматривается 
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

<dochub-anchor id="c4model_1"/>

### 1. Уровень I: Система в масштабе ее взаимодействия с пользователями и другими системами

<dochub-object type="context" subject="dochub.c4level1">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

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
в самих компонентах (см. [компоненты](#components)).  

<dochub-anchor id="c4model_2"/>

### 2. Уровень II: Взаимосвязанные контейнеры
<dochub-object type="context" subject="dochub.c4level2">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

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
<dochub-anchor id="c4model_3"/>

### 3. Уровень III: Взаимосвязанные компоненты
<dochub-object type="context" subject="dochub.c4level3">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

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

<dochub-anchor id="c4model_3_1"/>

#### 3.1. Консолидация манифестов:
<dochub-object type="context" subject="dochub.manifest_build">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

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

<dochub-anchor id="c4model_3_2"/>

#### 3.2. Парсинг и представление манифестов:
<dochub-object type="context" subject="dochub.manifest_presentation">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

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

<dochub-anchor id="c4model_3_3"/>

#### 3.3. Представление документов
<dochub-object type="context" subject="dochub.render_doc">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

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
<dochub-anchor id="c4model_4"/>

### 4. Уровень IV: дополнительные сведения о дизайне архитектурных элементов
<dochub-object type="context" subject="dochub.sequence">
    [ТУТ ДОЛЖНА БЫТь СХЕМА]
</dochub-object>

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

<dochub-anchor id="aspects"/>

## Архитектурные аспекты (aspects)

Аспекты позволяют выделить архитектурные компоненты реализующие определенный архитектурный аспект. Например, бизнес-функцию.
В отличие от [контекстов](#contexts), аспекты указываются непосредственно в [компонентах](#components). Т.е. именно в 
компоненте указывается какой из аспектов им реализуется. В манифесте аспекта указываются общие сведения о нем:

Описание контекста в манифесте [/manifest/aspects.yaml](/manifest/aspects.yaml):
```yaml
aspects:                                # Архитектурные аспекты
    gitlab.auth:
        title: Авторизация GitLab       # Название аспекта
        location: DocHub/Авторизация    # Размещение аспекта в навигационном дереве
    ...
```

В компоненте указывается, какие аспекты он реализует:
```yaml
title: DocHub       # Название компонента
entity: rectangle   # Сущность компонента из PlantUML (https://plantuml.com/ru/deployment-diagram)
  ...
aspects:            # Аспекты, которе реализует компонент
  - gitlab.auth
  - dochub.contexts
  - dochub.aspects
  - dochub.docs
  - dochub.radar
  - dochub.navigation
  ...
```

При выборе аспекта в меню открывается его карточка. Карточка содержит расширенные сведения об аспекте, а также
контексты в которых аспект встречается. Независимо от контекста аспект не визуализируется. Аспект может встречаться 
на различных уровнях рассмотрения архитектуры (в разных [контекстах](#contexts)). Визуализация вне контекстов привела
бы к смешению уровней и конфликту представления на диаграмме.

Пример представления аспекта:
<dochub-object type="aspect" subject="gitlab.auth" style="border: #ccc 1px solid">
    [ТУТ ДОЛЖЕНА БЫТь КАРТОЧКА АСПЕКТА]
</dochub-object>

<dochub-anchor id="docs"/>

## Документы (docs)


<dochub-anchor id="forms"/>

## Формы (forms) 

## Ссылки
