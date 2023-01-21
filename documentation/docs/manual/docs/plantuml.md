# PlantUML

Представляет собой исходные код на языке PlantUML.

```plantuml
@startuml
    participant Participant as Foo
    actor       Actor       as Foo1
    boundary    Boundary    as Foo2
    control     Control     as Foo3
    entity      Entity      as Foo4
    database    Database    as Foo5
    collections Collections as Foo6
    queue       Queue       as Foo7
    Foo -> Foo1 : To actor
    Foo -> Foo2 : To boundary
    Foo -> Foo3 : To control
    Foo -> Foo4 : To entity
    Foo -> Foo5 : To database
    Foo -> Foo6 : To collections
    Foo -> Foo7 : To queue
@enduml
```

Пример рендеринга документа:

![Документ](@document/dochub.example.pml)

[Далее](/docs/dochub.mermaid)