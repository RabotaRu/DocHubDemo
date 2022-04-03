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

|Переменная                              | Описание                                                                    |
|----------------------------------------|-----------------------------------------------------------------------------|
| **VUE_APP_DOCHUB_ROOT_MANIFEST**       | URI в формате DocHub на корневой манифест.                                  |
|                                        | Пример: manifest/root.yaml                                                  |
|                                        | Пример: gitlab:836:master@root.yaml                                         |
|                                        |                                                                             |
| **VUE_APP_DOCHUB_ROOT_DOCUMENT**       | Идентификатор главного документа (будет выводится на главной странице)      |
|                                        | Пример: main_document  По умолчанию: dochub_welcome                         |
|                                        |                                                                             |
| **VUE_APP_DOCHUB_GITLAB_URL**          | Если используется GitLab в качестве хранилища, в переменной указывается     |
|                                        | его URL адрес.                                                              |
|                                        | Пример: https://gitlab.local/                                               |
|                                        |                                                                             |
| **VUE_APP_DOCHUB_APP_ID**              | Application ID GitLab.                                                      |
|                                        | Пример: 21d85...350e5                                                       |
|                                        |                                                                             |
| **VUE_APP_DOCHUB_CLIENT_SECRET**       | Client secret GitLab.                                                       |
|                                        | Пример: 437d0...0a102                                                       |
|                                        |                                                                             |
| **VUE_APP_DOCHUB_APPEND_dochub.docs**  | Если "y", подключает манифест DocHab расположенный                          |
|                                        | \[хост]/manifest/root.yaml                                                  |
|                                        |                                                                             |
| **VUE_APP_DOCHUB_MODE=plugin**         | Если "plugin" сборка осуществляется для использования в IDEA плагине        |
|                                        | https://github.com/RabotaRu/DocHubIdeaPlugin                                |
