# создать папку для проекта
mkdir ansible
cd ansible

# создать файл inventory или хост файл

nano hosts.txt
```
ip1
ip2
```

Запустить пинг на все сервера из файла hosts.txt
ansible all -m ping

посмотреть Inventory
ansible-inventory --list
ansible-inventory --graph

Запустить проверку конфигураций
ansible all -m setup
ansible staging_servers -m setup

Запустить команду на удалённых серверах
ansible all -m shell -a "uptime"

Запустить команду на удалённых серверах без переменных окружающей среды
ansible all -m command -a "uptime"

Скопировать файл на все сервера
ansible all -m copy -a "src=test.txt dest=/root mode=777"

Флаг Become -> -b (аналог sudo) - указывается в конце команды
ansible all -m copy -a "src=test.txt dest=/root mode=777" -b

Удаление файла
ansible all -m file -a "path=/root/test.txt state=absent" -b

Скачать файл из интернета
ansible staging_servers -m get_url -a "url=url dest=/root"

Обновить пакеты:
ansible staging_servers -m apt -a "update_cache=yes cache_valid_time=3600 force_apt_get=yes"

Установить пакет
ansible staging_servers -m apt -a "name=curl state=present"

Удалить пакет
ansible staging_servers -m apt -a "name=curl state=removed"

Запустить сервис
ansible staging_servers -m service -a "name=service_name state=started enabled=yes"

Дебагинг используется через флаг -v, -vv и тд
ansible all -m shell -a "uptime" -v

Вывести все модули ansible
ansible-doc -l

Запустить playbook
ansible-playbook playbook1.yml

Создать роль
mkdir roles
cd roles
ansible-galaxy init deploy_web_server
#### далее будет создана структура с пустыми файлами
####  документация, чем должны быть заполнены файлы https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html
####  Роли - это сущности, которые разбивают плейбуки по маленьким деталям
В playbook указывается так:
roles:
  - { deploy_web_server, when: ansible_system == 'Linux' }

Использование внешних переменных
ansible-playbook playbook1.yml --extra-var "my_var=test_var my_var2=test_var2"

Импорт и включения файлов
include: playbook7_create_folders.yml
import: playbook7_create_folders.yml
####  include запускается толкьо в момент, когда доходит до данной инструкции,
####  import запускается в самом начала запуска

Отдать команду на выполнение только отдельному серверу в .yml файле (вместо использования групп) 
delegate_to: linux_name

Создать файл с чувствительными данными:
ansible-vault create secrets.txt
1234

Посмотреть файл с чувствительными данными:
ansible-vault view secrets.txt

Редактировать файл с чувствительными данными:
ansible-vault edit secrets.txt

Зашифровать playbook
ansible-vault encrypt playbook1.yml

Запустить зашифрованный playbook (надо вводить пароль вручную)
ansible-playbook playbook1.yml --ask-vault-pass

Запустить зашифрованный playbook без ручного ввода пароля
nano mypass.txt
----
1234_pwd
----
ansible-playbook playbook1.yml --vault-password-file mypass.txt

Зашифровать переменные в .yml файле
ansible-vault encrypt_string

