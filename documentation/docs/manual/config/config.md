# Файлы переменных среды исполнения

Файлы конфигурации среды исполнения:
```text
.env                # загружается во всех случаях
.env.local          # загружается во всех случаях, игнорируется git
.env.[mode]         # загружается только в указанном режиме работы
.env.[mode].local   # загружается только в указанном режиме работы, игнорируется git
```
[Больше информации](https://cli.vuejs.org/ru/guide/mode-and-env.html) 

# Переменные среды исполнения

|Переменная                                     | Описание                                                                    |
|-----------------------------------------------|-----------------------------------------------------------------------------|
| **VUE_APP_DOCHUB_ROOT_MANIFEST**              | URI в формате DocHub на корневой манифест.                                  |
|                                               | Пример: VUE_APP_DOCHUB_ROOT_MANIFEST = manifest/root.yaml                   |
|                                               | Пример: VUE_APP_DOCHUB_ROOT_MANIFEST = gitlab:836:master@root.yaml          |
|                                               |                                                                             |
| **VUE_APP_DOCHUB_ROOT_DOCUMENT**              | Идентификатор главного документа (будет выводится на главной странице)      |
|                                               | По умолчанию: dochub_welcome                                                |
|                                               | Пример: VUE_APP_DOCHUB_ROOT_DOCUMENT = main_document                        |
|                                               |                                                                             |
| **VUE_APP_DOCHUB_GITLAB_URL**                 | Если используется GitLab в качестве хранилища, в переменной указывается     |
|                                               | его URL адрес.                                                              |
|                                               | Пример: VUE_APP_DOCHUB_GITLAB_URL = https://gitlab.local/                   |
|                                               |                                                                             |
| **VUE_APP_DOCHUB_APP_ID**                     | Application ID GitLab.                                                      |
|                                               | Пример: VUE_APP_DOCHUB_APP_ID = 21d85...350e5                               |
|                                               |                                                                             |
| **VUE_APP_DOCHUB_CLIENT_SECRET**              | Client secret GitLab.                                                       |
|                                               | Пример: VUE_APP_DOCHUB_CLIENT_SECRET = 437d0...0a102                        |
|                                               |                                                                             |
| **VUE_APP_DOCHUB_APPEND_DOCHUB_DOCS**         | Если "y", подключает манифест DocHab расположенный                          |
|                                               | \[хост]/manifest/root.yaml                                                  |
|                                               |                                                                             |
| **VUE_APP_DOCHUB_MODE=plugin**                | Если "plugin" сборка осуществляется для использования в IDEA плагине        |
|                                               | https://github.com/RabotaRu/DocHubIdeaPlugin                                |
|                                               |                                                                             |
| **VUE_APP_DOCHUB_APPEND_DOCHUB_METAMODEL**    | Если "у" подключаются ядровые правила контроля метомодели DocHub            |
|                                               | По умолчанию: y                                                             |
|                                               | Пример: VUE_APP_DOCHUB_APPEND_dochub.metamodel = n                          |

