# zabbix-user-group.role

Роль для создания пользователей и групп в Zabbix через API.

## Features

1) Сначала создаются группы, затем пользователи
2) При создании пользователя Zabbix требует, чтобы он состоял хотя бы в одной группе, поэтому `zbx_us_users_list.user_groups` нужно обязательно определить, иначе роль будет завершаться ошибкой
3) При попытке прогнать роль с уже добавленным пользователем или группой - данные заменены новыми не будут, Zabbix просто вернет сообшение о том, что пользователь/группа с таким `user_alias`/`group_name` уже созданы

## Zabbix API

1) Пользователи: https://www.zabbix.com/documentation/3.0/ru/manual/api/reference/user
2) Группы: https://www.zabbix.com/documentation/3.0/ru/manual/api/reference/usergroup

## Example

```yaml
zabbix_api_url: "http://zbx.server.ru/api_jsonrpc.php"
zabbix_api_user: "APIuser"
zabbix_api_password: "APIuserPass"


zbx_us_groups_list:

# Группа без пользователей
  - group_name: "Group1"
    group_debug_mode: "0"
    group_gui_access: "0"
    group_users_status: "0"
    # Права группы на группы хостов
    group_hosts_groups:
      - name: "Linux servers"
        permission: "2"
      - name: "Some servers"
        permission: "0"

# Группа с r/w правами на хосты "Other servers" и пользователями
  - group_name: "Group2"
    group_debug_mode: "0"
    group_gui_access: "0"
    group_users_status: "0"
    # Права группы на группы хостов
    group_hosts_groups:
      - name: "Other servers"
        permission: "3"
    # Пользователи в группе
    group_users_aliases:
      - "user1"
      - "user2"
      - "user3"


zbx_us_users_list:

  - user_alias: "dokienko"
    user_pass: "123456"
    user_name: "Denis"
    user_surname: "Dokienko"
    user_type: "3"
    user_groups:
      - "Group1"
      - "Group2"
```

## Some Vars

#### zbx_us_users_list

Переменная | 0 | 1 | 2 | 3
--- | --- | --- | --- | ---
`user_type` | - | Zabbix пользователь | Zabbix администратор | Zabbix супер-администратор

#### zbx_us_groups_list

Переменная | 0 | 1 | 2 | 3
--- | --- | --- | --- | ---
`debug_mode` | (по умолчанию) деактивирован | активирован | - | -
`gui_access` | (по умолчанию) использование метода аутентификации системы по умолчанию | использование внутренней аутентификации | деактивация доступа к веб-интерфейсу | -
`users_status` | (по умолчанию) активирована | деактивирована | - | -
`group_hosts_groups.permission` | доступ запрещен | - | доступ только чтение | доступ чтение-запись
