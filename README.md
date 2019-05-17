# Что это?

Ой, да тут всё просто: ansible, terraform. Поднять кластер из трёх серверов mongo, ограничить доступ через ufw (можно прописать ещё свой ip для упрощения). Результат работы выставить к репозиторию как PR, желательно прокомментировать процесс.


Решение.
Файл terraform.tf доработан таким образом, чтобы использовать установленный в Ubuntu 18.04 по умолчанию Python 3:
ansible_python_interpreter = /usr/bin/python3

Также добавлены строки для формирования файла /etc/hots, который затем копируется на все виртуальные машины в облаке.

Создан главный playbook - site.yml, запускающий два playbooks: playbook.yml и  mongo_master.yml.
Первый устанавливает Mongo на все машины, копирует конфигурационный файл mongod.conf.j2 (bindIp 0.0.0.0 ОБЯЗАТЕЛЬНО/ По умолчанию 127.0.0.1 не  разрешает добалять secondary replicas).
А также настройки firewall. Разрешен SSH отовсюду (не особо правильно, но все-таки песочница), а также открывает 27017 порт для Mongo. Также отовсюду. Также не очень красиво. И закрывает все остальные порты.

Mongo_master.yml выполняет инициализацию - rs.initiate(). И добавляет две secondary replicas - mongo-test2, mongo-test3.