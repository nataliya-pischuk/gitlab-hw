# Домашнее задание к занятию «Система мониторинга Zabbix»
Пищук Наталья Владимировна


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1 

Установите Zabbix Server с веб-интерфейсом.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
3. Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
4. Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.

#### Требования к результатам 
1. Прикрепите в файл README.md скриншот авторизации в админке.
2. Приложите в файл README.md текст использованных команд в GitHub.

У меня основная машина работы это Ubuntu 24.04. Zabbix устанавливала для нее
Источник: https://www.zabbix.com/ru/download?zabbix=7.4&os_distribution=ubuntu&os_version=24.04&components=agent&db=&ws=

Предварительно установила Postgress
Установите PostgreSQL:
sudo apt install postgresql

Добавление репозитория
sudo -s
wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu24.04_all.deb
dpkg -i zabbix-release_latest_7.4+ubuntu24.04_all.deb
apt update

Установка основных компонентов
apt install zabbix-server-pgsql zabbix-frontend-php php8.3-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agentsudo -u postgres createuser --pwprompt zabbix

sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix


Импортируем схему:
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

Задаем пароль в конфиге zabbix_server
sed -i 's/# DBPassword=/DBPassword=12345678/g' /etc/zabbix/zabbix_server.conf

![alt text](img/server host.JPG)

Запускаем Zabbix server, Zabbix agent и веб-сервер:
sudo systemctl restart zabbix-server apache2 
sudo systemctl enable zabbix-server apache2 

#### Авторизация в zabbix
![alt text](img/init.JPG)

---
# Задание 2
Установите Zabbix Agent на два хоста.

Процесс выполнения
Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.

# Решение
Клонировала свою виртуальную машину со всеми действующими настройками и установила связь между ними
На обеих машинах выполнила следующие действия:

sudo -s
Добавления репозитория
wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu24.04_all.deb
dpkg -i zabbix-release_latest_7.4+ubuntu24.04_all.deb

установка Zabbix агента
apt install zabbix-agent

Запуск процесса Zabbix агента и настройка его.

systemctl restart zabbix-agent
systemctl enable zabbix-agent
![alt text](img/start agent.JPG)

Корректировка файла zabbix_agentd.conf:
![alt text](img/agent.JPG)

### Результат
![alt text](img/Monitoring.JPG)
![alt text](img/result.JPG)

---
