# Домашнее задание к занятию "08.01 Введение в Ansible"

## Подготовка к выполнению
1. Установите ansible версии 2.10 или выше.
2. Создайте свой собственный публичный репозиторий на github с произвольным именем.
3. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

## Основная часть
1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении playbook'a.
### Ответ:
```
8.1/playbook# ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ******************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************
ok: [localhost]

TASK [Print OS] ************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] **********************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP *****************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.
### Ответ:
```
# ansible-playbook -i inventory/test.yml site.yml

PLAY [Print os facts] ******************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************
ok: [localhost]

TASK [Print OS] ************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}

TASK [Print fact] **********************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}

PLAY RECAP *****************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.
### Ответ:
Поднял два контейнера.

4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.
### Ответ:
```
# ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] ******************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************
ok: [ubuntu]
ok: [elastic1]

TASK [Print OS] ************************************************************************************************************************
ok: [elastic1] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "CentOS"
}

TASK [Print fact] **********************************************************************************************************************
ok: [elastic1] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP *****************************************************************************************************************************
elastic1                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   



```

5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились следующие значения: для `deb` - 'deb default fact', для `el` - 'el default fact'.
### Ответ:
Изменил файлы group_vars/deb/examp.yml и group_vars/el/examp.yml


6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.
### Ответ:
```
# ansible-playbook -i inventory/prod.yml site.yml

PLAY [Print os facts] ******************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************
ok: [ubuntu]
ok: [elastic1]

TASK [Print OS] ************************************************************************************************************************
ok: [elastic1] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "CentOS"
}

TASK [Print fact] **********************************************************************************************************************
ok: [elastic1] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *****************************************************************************************************************************
elastic1                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.
### Ответ:
```Команды:
ansible-vault encrypt group_vars/el/examp.yml
ansible-vault encrypt group_vars/el/examp.yml
С паролем 1.
```

8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.
### Ответ:
```ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
```

9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.
### Ответ:
```
ansible-doc -t connection -l
Подходящий - local.
```

10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.
### Ответ:
```
добавил:
local:
    hosts:
      localhost:
        ansible_connection: local
```

11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь что факты `some_fact` для каждого из хостов определены из верных `group_vars`.
### Ответ:
```
# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
Vault password:

PLAY [Print os facts] ********************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************
ok: [localhost]
ok: [ubuntu]
ok: [elastic1]

TASK [Print OS] **************************************************************************************************************************
ok: [localhost] => {
    "msg": "Ubuntu"
}
ok: [ubuntu] => {
    "msg": "CentOS"
}
ok: [elastic1] => {
    "msg": "CentOS"
}

TASK [Print fact] ************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}
ok: [elastic1] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *******************************************************************************************************************************
elastic1                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  

```

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.
### Ответ:
```

```