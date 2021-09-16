# Импорт манифестов (imports)
Существует возможность описать всю архитектуру в одном манифесте. Но работа с единым файлом имеет
ряд недостатков:
1. В объемном файле сложно контролировать вносимые изменения;
2. Устранение конфликтов усложняется, т.к. над одним файлом могут работать срезу несколько человек;
3. Обозримость описания архитектуры значительно ухудшается.

Рекомендуется использовать структуру файлов и папок для устранения этих недостатков. Например,
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

[Далее](/docs/dochub_namespaces)
