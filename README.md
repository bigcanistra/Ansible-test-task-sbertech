# Ansible-test-task-sbertech

Написать playbook с учетом Best Practices Ansible - https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html, который настраивает окружение двух виртуальных машин под дальнейшую работу приложений.
На каждой ВМ разные операционные системы: RHEL 7.9, Ubuntu 18.04.

# 1 задание.

Перед настройкой необходимо осуществить ряд проверок:

- [x] наличие 10ГБ свободного места в корневой ФС
- [ ] проверка, что ФС /tmp имеет параметр exec в /etc/fstab
- [ ] В случае не прохождения проверки выводить человеко-читаемую ошибку в лог Ansible.

# 2 задание.

На каждую ВМ требуется устанавливать разный список пакетов:

```
RHEL:
    "expect"
    "jq"
    "jsoncpp"
    "libcurl"
    "libyaml"
    "logrotate"
    "nmap"
    "openssl"
    "perl-DBD-Pg"
    "perl-DBI"
    "perl-Time-HiRes"
    "python3-pip"
    "python36"
    "python36-devel"
    "python36-virtualenv"
    "rsyslog"
    "uuid"
    
UBUNTU:
    "autoconf"
    "automake"
    "jq"
    "libcrack2"
    "libcurl"
    "libjsoncpp"
    "libtool"
    "libyaml-0-2"
    "logrotate"
    "openssl"
    "perl-openssl-defaults"
    "python3-pip"
    "python3.6"
    "python3.6-dev"
```

# 3 задание.

После проверок и установки пакетов необходимо шаблонизировать конфигурационный файл в зависимости от кол-ва ядер и оперативной памяти:

```
postgresql: 
    parameters: 
        max_connections : кол-во ядер умноженное на 5 
        max_locks_per_transaction: '64' 
        max_worker_processes: кол-во ядер умноженное на 3 
        max_prepared_transactions: если ОС Ubuntu, то (кол-во ядер умноженное на 6), иначе (кол-во ядер умноженное на 4) 
        wal_level: 'replica' 
        wal_log_hints: on 
        track_commit_timestamp: off 
        wal_keep_size: '8GB' 
        logical_decoding_work_mem: кол-во оперативной памяти делённое на 4 (единица измерения ГБ) 
        auth_activity_period: '60
```
В процессе работы плэйбука создать конфиг из шаблона по пути /etc/postgres/test_config.conf с владельцем УЗ postgres и разрешением на изменение только postgres, а чтение любому пользователю ОС.