# Архитектурные исключения (exceptions)

Исключения непосредственно связаны с результатом работы архитектурных [валидаторов](/docs/dochub.rules.validators). 
Они позволяют игнорировать некоторые выявленные ошибки, с указанием онования для этого.

Пример исключения:
```yaml
...
rules:
  exceptions:                                               # Исключения для валидаторов
    "source-component-dochub.front.spa.blank.doc.markdown": # UID ошибки для исключения
      reason: >                                             # Причина исключения
        Пример исключения для указания исходных кодов
      cause: /docs/dochub.exceptions.examples.doc_markdown  # Документ-основание исключения...
```

В примере создано исключение для валидатра "dochub.source".

[Далее](/docs/dochub.technologies) 

