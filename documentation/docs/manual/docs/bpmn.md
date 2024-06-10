# BPMN

Поддерживается BPMN нотация описания бизнес-процессов с использованием [bpmnjs](https://bpmn.io/). 

## Пример рендеринга процесса из xml файла
Представление:
![BPMN](@document/dochub.example.bpmn)


Манифест:
```yaml
...
  dochub.example.bpmn:
    location: DocHub/Руководство/Документы/BPMN/Простой пример  # Размещение документа в меню
    type: bpmnjs                                                # Тип документа
    source: examples/bpmn.xml                                   # Файл с xml описанием диаграммы
...
```

Содержимое файла examples/bpmn.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions 
    xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
    xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI"
    xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI"
    xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    id="sid-38422fae-e03e-43a3-bef4-bd33b32041b2"
    targetNamespace="http://bpmn.io/bpmn"
    exporter="bpmn-js (https://demo.bpmn.io)"
    exporterVersion="17.6.2">
  <process id="Process_1" isExecutable="false">
    <startEvent id="StartEvent_1y45yut" name="hunger noticed">
      <outgoing>SequenceFlow_0h21x7r</outgoing>
    </startEvent>
    <task id="Task_1hcentk" name="choose recipe">
      <incoming>SequenceFlow_0h21x7r</incoming>
      <outgoing>SequenceFlow_0wnb4ke</outgoing>
    </task>
    <sequenceFlow id="SequenceFlow_0h21x7r" sourceRef="StartEvent_1y45yut" targetRef="Task_1hcentk" />
    <exclusiveGateway id="ExclusiveGateway_15hu1pt" name="desired dish?">
      <incoming>SequenceFlow_0wnb4ke</incoming>
    </exclusiveGateway>
    <sequenceFlow id="SequenceFlow_0wnb4ke" sourceRef="Task_1hcentk" targetRef="ExclusiveGateway_15hu1pt" />
  </process>
  <bpmndi:BPMNDiagram id="BpmnDiagram_1">
    <bpmndi:BPMNPlane id="BpmnPlane_1" bpmnElement="Process_1">
      <bpmndi:BPMNShape id="StartEvent_1y45yut_di" bpmnElement="StartEvent_1y45yut">
        <omgdc:Bounds x="152" y="102" width="36" height="36" />
        <bpmndi:BPMNLabel>
          <omgdc:Bounds x="134" y="145" width="73" height="14" />
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape id="Task_1hcentk_di" bpmnElement="Task_1hcentk">
        <omgdc:Bounds x="240" y="80" width="100" height="80" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape id="ExclusiveGateway_15hu1pt_di" bpmnElement="ExclusiveGateway_15hu1pt" isMarkerVisible="true">
        <omgdc:Bounds x="395" y="95" width="50" height="50" />
        <bpmndi:BPMNLabel>
          <omgdc:Bounds x="388" y="152" width="65" height="14" />
        </bpmndi:BPMNLabel>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNEdge id="SequenceFlow_0h21x7r_di" bpmnElement="SequenceFlow_0h21x7r">
        <omgdi:waypoint x="188" y="120" />
        <omgdi:waypoint x="240" y="120" />
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge id="SequenceFlow_0wnb4ke_di" bpmnElement="SequenceFlow_0wnb4ke">
        <omgdi:waypoint x="340" y="120" />
        <omgdi:waypoint x="395" y="120" />
      </bpmndi:BPMNEdge>
    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>
</definitions>
```

## Дизайн процессов

Поддерживается возможность редактирования BPMN диаграмм в визуальном редакторе. 
В настоящий момент эта возможность доступна в [плагине к IDE IDEA](https://plugins.jetbrains.com/plugin/18518-dochub-architecture-as-code).
Также есть возможность редактировать диаграммы на сайте проекта [bpmnjs](https://demo.bpmn.io/s/start).

**ВАЖНО:** В перспективе редактирование диаграмм будет доступно также на портале, а также разнообразие нотаций 
из проекта bpmnjs будет значительно расширено.

Видео-инструкция по редактированию диаграмм:
![Редактирование BPM диаграмм в IDEA](https://youtu.be/qQzsHuYxNzg)


## Анализ процессов

DocHub может обращаться к описанию процессов в XML формате аналогично YAML и JSON. Для этого необходимо указать XML
файл как источник данных. Например, рассмотрим анализ процесса:

![BPMN](@document/dochub.example.bpmn.full)

Выявим реестр задач определенных в процессе и выразим его в таблице:

```yaml
docs:
  ...
  dochub.example.bpmn.analyse:
    origin: examples/pizza-collaboration.xml  # Указываем источником оригинальных данных XML файл
    source: >                                 # Обрабатываем XML
      (
          [$."semantic:definitions"."semantic:process"."semantic:task"."$"]
      )
    type: table                               # Тип документа "таблица"
    headers:                                  # Заголовки таблицы
      - value: id
        text: Идентификатор
        sortable: true
        align: left
        width: 20%
      - value: name
        text: Название задачи
        sortable: true
        align: left
  ...
```

Результат:

![BPMN](@document/dochub.example.bpmn.analyse)