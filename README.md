# Домашнее задание к занятию 17.5 «Тестирование roles» - Падеев Василий



## Подготовка к выполнению

1. Установите molecule и его драйвера: `pip3 install "molecule molecule_docker molecule_podman`.

![answer1](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/4.png)

2. Выполните `docker pull aragast/netology:latest` —  это образ с podman, tox и несколькими пайтонами (3.7 и 3.9) внутри.

## Основная часть

Ваша цель — настроить тестирование ваших ролей. 

Задача — сделать сценарии тестирования для vector. 

Ожидаемый результат — все сценарии успешно проходят тестирование ролей.

### Molecule

1. Запустите  `molecule test -s ubuntu_xenial` (или с любым другим сценарием, не имеет значения) внутри корневой директории clickhouse-role, посмотрите на вывод команды. Данная команда может отработать с ошибками или не отработать вовсе, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу И из чего может состоять сценарий тестирования.

![answer2](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/5.png)

2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи `molecule init scenario --driver-name docker`.

![answer3](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/6.png)

3. Добавьте несколько разных дистрибутивов (oraclelinux:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.
Найдены ошибки, отсутствия role_name и namespace meta/mail.yml   
![answer4](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/7.png)

После исправления критических ошибок найдено не было.  
![answer5](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/8.png)  
![answer6](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/9.png)  

Предупреждения:  
- Skipping, missing the requirements file. — отсутствует файл requirements.yml, который содержит зависимости для Ansible ролей.  
- Skipping, cleanup playbook not configured. — не настроен playbook для очистки.  
- Skipping, side effect playbook not configured. —  playbook не настроен для тестов или операций.  
- Skipping, verify playbook not configured. — отсутствует playbook для проверки состояния системы после выполнения основной роли.  
- Skipping, cleanup playbook not configured. — отсутствует playbook для завершения тестов.

4. Добавьте несколько assert в verify.yml-файл для  проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.). 

[verify.yml](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/playbook/roles/Vector/molecule/default/verify.yml)


5. Запустите тестирование роли повторно и проверьте, что оно прошло успешно.

Ошибки:  
5.1 В контейнере oraclelinux:8 установлен слишком старый Python, не поддерживающий синтаксис from __future__ import annotations, который используется в модулях Ansible.   
5.2 Установка Vector написана для ОС CentOS, а проверка выполняется в контейнере Ubuntu   
![answer7](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/13.png)  
![answer8](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/14.png)  
![answer9](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/15.png)  

6. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.  

[vector](https://github.com/Vasiliy-Ser/vector-role/releases/tag/v1.3.0)  

### Tox

1. Добавьте в директорию с vector-role файлы из [директории](./example).  
2. Запустите `docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash`, где path_to_repo — путь до корня репозитория с vector-role на вашей файловой системе.  
3. Внутри контейнера выполните команду `tox`, посмотрите на вывод.  

![answer10](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/16.png)
![answer11](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/19.png)

 Появляется проблему с  отсутствием пакета six, который необходим для работы Ansible,  добавим в [tox-requirements.txt](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/playbook/roles/Vector/tox-requirements.txt). Также обновим конфигурацию [tox.ini](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/playbook/roles/Vector/tox.ini).  
![answer12](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/20.png)

При проверки обнаружено отсутствие драйвера molecule-docker  

![answer13](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/21.png)  
![answer14](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/22.png)  

Но ошибки все равно сохраняются  

![answer15](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/23.png)

5. Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость.

Буду использовать драйвер podman

![answer16](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/24.png)  
![answer17](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/25.png)

Библиотеки не полные. Выполнил переустановку podman

![answer18](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/26.png)  
![answer19](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/27.png)  
![answer20](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/28.png)  
![answer21](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/29.png)  
![answer22](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/30.png)  

6. Пропишите правильную команду в `tox.ini`, чтобы запускался облегчённый сценарий.

Для облегченного сценария выполним проверку синтаксиса [tox.ini](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/6cd09279b73c8a778c228a21da31a95e96a0c442/playbook/roles/Vector/tox.ini)  

8. Запустите команду `tox`. Убедитесь, что всё отработало успешно.

![answer23](https://github.com/Vasiliy-Ser/Ansible_role_testing_17.5/blob/982dd09546a1bc1b67ff14fe88918377ae64698d/png/31.png)  

9. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

[vector](https://github.com/Vasiliy-Ser/vector-role/releases/tag/v1.4.1)  

После выполнения у вас должно получится два сценария molecule и один tox.ini файл в репозитории. Не забудьте указать в ответе теги решений Tox и Molecule заданий. В качестве решения пришлите ссылку на  ваш репозиторий и скриншоты этапов выполнения задания. 

## Необязательная часть

1. Проделайте схожие манипуляции для создания роли LightHouse.
2. Создайте сценарий внутри любой из своих ролей, который умеет поднимать весь стек при помощи всех ролей.
3. Убедитесь в работоспособности своего стека. Создайте отдельный verify.yml, который будет проверять работоспособность интеграции всех инструментов между ними.
4. Выложите свои roles в репозитории.

В качестве решения пришлите ссылки и скриншоты этапов выполнения задания.

---

