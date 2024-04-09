# Шаблонизация SVG файлов


Оригинальный шаблон:
![Шаблон](templates/test.svg)

Результат применения данных к шаблону:
```yaml
 docs:
   dochub.plugins.svg_file:
     title: Проверка svg
     type: svg
     location: DocHub/Руководство/Плагины/Примеры/SVG/Пример svg
     template: templates/test.svg
     source: >
       (
        {
          "component1": "Название компонента 1",
          "line1_text1": "Текст для стрелки"
        }
       )
```

Результат: 
```
![svg-документ](@document/dochub.plugins.svg_file)
```

![svg-документ](@document/dochub.plugins.svg_file)


# Проверка заполнения параметров в шаблоне

Есть возможность проверять корректность заполнения шаблона. Например:

```yaml
  dochub.plugins.svg_file_error:
    title: Проверка svg
    type: svg
    template: templates/test.svg
    source: >
      (
        {
          "component1": "Название компонента 1",
          "component2": "Название компонента 2" 
        }
      )
```

В примере намеренно допущены ошибки:
1. Указан несуществующий в шаблоне параметр "component2";
2. Не указан необходимый параметр "line1_text1".


Для выполнения проверки указывается специальный параметр check=1.

```
![svg-документ](@document/dochub.plugins.svg_file_error?check=1)
```

В результате будет список не найденных параметров в самом шаблоне и в запросе:

![svg-документ](@document/dochub.plugins.svg_file_error?check=1)



