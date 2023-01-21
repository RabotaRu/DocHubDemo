# Интерфейсы ядра

Ядро DocHub предоставляет два интерфейса для взаимодействия плагинов с ним:

## 1. Глобальная переменная DocHub

window.DocHub - глобальная переменная содержащая коллекцию менеджеров, управляющих базовыми сущностями DocHub.

### DocHub.documents.register(type: String, component: Component)

Регистрирует новый тип документов. Принимает параметры:

* **type** - Название типа документа;
* **component** - VUE2 компонент, который будет использован для рендеринга документов указанного типа.

Пример использования:

```JavaScript
import doc from './components/HTMLDocument.vue';

DocHub.documents.register('html', doc);
```

## 2. Параметры компонентов documents

### profile: Object

В параметре передается объект, содержащий данные участка архитектурного кода, конфигурирующего представление 
документа. Например, код:

```yaml
docs:
  ...
  dochub.plugins.example:
    location: DocHub/Руководство/Плагины/Пример
    type: html
    source: examples/example.html    
```

вызовет компонент плагина, зарегистрированного для типа документа "html", которому будет передана 
в параметре "manifest" структура:

```json
{
    "location": "DocHub/Руководство/Плагины/Пример",
    "type": "html",
    "source": "examples/example.html"
}
```

Пример использования:
```JavaScript
props: {
    ...
    profile: {
        type: Object,
        required: true
    },
    ...
}
```

### params: Object

Содержит параметры переданные в документ.

Пример встраивания документа в [markdown](/docs/dochub.markdown):

```Markdown
![Пример документа плагина](@document/dochub.plugins.example?param1=foo1&param2=foo2)
```

В этом случае свойство будет содержать структуру:
```json
{
    "param1": "foo1",
    "param2": "foo2"
}
```

Параметры также могут быть переданы в ссылке, например:

[/docs/dochub.plugins.example?param1=foo1&param2=foo2](/docs/dochub.plugins.example?param1=foo1&param2=foo2)

Свойство params будет иметь значение идентичное первому случаю. 

### path: String

Содержит путь к объекту документа. 

Код:

```yaml
docs:
    ...
    dochub.plugins.example:
        location: DocHub/Руководство/Плагины/Пример
        type: html
        headers:
        head1:
            type: test
        source: examples/example.html
    ...
```
Вызовет компонент плагина с параметром path содержащим строку "/docs/dochub.plugins.example".

### getContent: function(URL: String)

В архитектурной кодовой базе указываются относительные пути к файлам.
Для успешного получения контента этих файлов необходимо использовать функцию getContent передающуюся 
в качестве параметра компонента.

Функция принимает в качестве параметра URL и возвращает Promise.

Рекомендуется также использовать эту функцию для доступа к любым иным ресурсам по http/https. 

Пример использования:
```JavaScript
props: {
    ...
    getContent: {
        type: Function,
        required: true
    },
    profile: {
        type: Object,
        required: true
    }
    ...
},
methods: {
    ...
    refresh() {
        ...
        this.getContent(this.profile.source)
        .then((response) => {
            console.info(`Успешный успех!`, response.data);
        })
        .catch((error) => {
            console.error(`Ошибка выполнения запроса [${this.profile.source}]`, error);
        });
        ...
    }
    ...
}
```

### pullData: function(expression: String, self: Object, params: Object, context: Any)

Функция позволяет выполнить запрос к архитектуре для получения необходимых данных.
Все параметры являются опциональными. При их отсутствии используются дефолтные 
значения.

* **expression** - [JSONata](https://jsonata.org/) запрос к архитектуре или идентификатор [DataSet](/docs/dochub.datasets). 
По умолчанию содержит значение из this.profile.source.
* **self** - Передает в запрос объект для переменной $self. По умолчанию содержит объект this.profile.
* **params** - Передает в запрос объект для переменной $params. По умолчанию заполняется параметрами переданными в документ this.params.
* **context** - Контекст для выполнения запроса. По умолчанию контекстом являются данные архитектуры.

**ВАЖНО:** Необходимо использовать только эту функцию для доступа к данным архитектуры. Иной путь получения 
данных может приводить к неожиданным результатам.

Пример простейшего использования:
```JavaScript
props: {
    ...
    params: {
        type: Object,
        default: null
    },
    profile: {
        type: Object,
        required: true
    },
    pullData: {
        type: Function,
        required: true
    }
    ...
},
data() {
    return {
        data: []
    };
},
methods: {
    ...
    refresh() {
        ...
        this.pullData()
        .then((data) => this.data = data)
        .catch((error) => console.error(`Ошибка получения данных`, error));
        ...
    }
    ...
}
```

В примере функция выполнит запрос содержащийся в поле this.profile.source к данным архитектуры,
с параметром $self равным this.profile и $params равным this.params.

Пример кастомного запроса к данным архитектуры:

Пример простейшего использования:
```JavaScript
data() {
    return {
        componentsCount: []
    };
},
methods: {
    ...
    refresh() {
        ...
        this.pullData("($count(components.*))")
        .then((data) => this.componentsCount = data)
        .catch((error) => console.error(`Ошибка получения данных`, error));
        ...
    }
    ...
}
```

В данном случае функция выполнит JSONata запрос, который вернет количество описанных компонентов.
Обратите внимание, что запрос должен всегда обрамляться круглыми скобками. 

