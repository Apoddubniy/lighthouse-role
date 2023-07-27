ansible - lighthouse + nginx
=========

Устанавливает и конфигурирует lighthouse  и nginx

Support platform
--------

| Name | Version |
| :----: | :-----:|
| Centos| 7,8|
| Rhel | 7,8 |

Role Variables
--------------
| Переменная |                   Описание                    |             По умолчанию              |
|:----------:|:---------------------------------------------:|:-------------------------------------:|
| light_repo |             Ссылка на репозиторий             | "https://github.com/vkcom/lighthouse" |
| light_path |        Путь для установки дистрибутива        |     "/usr/share/nginx/html/light"     |



Description role
--------

#### Tasks Nginx
Для установки сервиса Nginx, и прочего ПО в виде git, необходимо подключить для системы centos 7 epel репозиторий.   
PLAY состоит из следующих пунктов:
* С помощью pre_tasks подключаем epel репозиторий `ansible.builtin.yum_repository` с сылкой на него. 
* Следующим пунктом идет установка nginx и git с помощью модуля `ansible.builtin.yum`

#### Tasks Lighthouse
Для установки Lighthouse необходимы предустановленные сервисы из tasks Nginx
* Хендлер перезапустит веб-сервер nginx (`ansible.builtin.service` /`name: nginx / state: restarted`), после оповещения об изменении конфигурации при помощи задачи `- name: Create light config`, которая из шаблона `template:` изменяет конфигурацию сервиса.
* В конфигурации шаблона [nginx.j2](templates/nginx.j2), в секцию `server` добавлена переменная `root         {{ light_path }};`, которая принимает значение из [light_path](group_vars/light/vars.yml)
* Скачивание lighthouse с помощью модуля `git`, в переменных указаны ссылки и пути:
  * Переменная `{{ light_repo }}` ссылка на репозиторий из файла [vars/main/light_repo](vars/main.yml)
  * Переменная `{{ light_path }}` папка на файловой системе конечного хоста [vars/main/light_path](vars/main.yml)
* Изменены правила файервола, для открытия входящих соединений на 80 (http) порт, следующей конструкцией:
```commandline
    - name: FIREWALL | Add Rules
      changed_when: false
      become: true
      shell: |
        firewall-cmd --permanent --add-service=http
        firewall-cmd --reload
```
Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```yaml
    - hosts: servers
      roles:
         - role: lighthouse-role
```
License
-------

MIT

Author Information
------------------

Aleksandr Poddubniy