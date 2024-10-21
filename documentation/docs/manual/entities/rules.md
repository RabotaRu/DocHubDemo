# Ролевая модель

Ролевая модель - позволяет разграничивать доступы к элементам архитектуры. 
Управление ролями пользователей происходит на стороне keycloack.

Для включения работы с ролевой моделью требуется установить следующий флаг:
VUE_APP_DOCHUB_ROLES_MODEL=y

Описание ролей осуществляется в корневом файле roles.yaml
Пример описания правил доступа:
```
 roles:
   users:
     - '^kadzo.v2023.data_objects*$' // Правило представляет собой регулярное выражение
     - '^ecogroup.berezka.data_objects[a-zA-Z\._0-9]*$'
   uek:
     - '^kadzo\.v2023\.kb_systems[a-zA-Z\._0-9]*$'
     - '^ecogroup.berezka.kb[a-zA-Z\._0-9]*$'
   default:
     - '^kadzo\.v2023\.data_objects*$'
```
VUE_APP_DOCHUB_ROLES=file:///workspace/sberauto/roles.yaml

Если пользователь имеет несколько ролей, то наборы правил объединяются.

Для работы ролевой модели требуется дополнительно указать в файле .env несколько параметров:

```
Указываем путь до сервера аутентификации с указанием realms
VUE_APP_DOCHUB_AUTHORITY_SERVER=https://dochub-server.ru/realms/dochub
Указываем client id, например: dochub
VUE_APP_DOCHUB_AUTHORITY_CLIENT_ID={CLIENTID}
{PUBLIC KEY} - указываем public key, смотрим настройки keycloack
VUE_APP_DOCHUB_AUTH_PUBLIC_KEY=-----BEGIN PUBLIC KEY-----{PUBLIC KEY}-----END PUBLIC KEY-----
```
