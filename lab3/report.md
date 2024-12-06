University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34202  
Author: Gusevskaya Arina  
Lab: Lab3
<br/>Date of create: 28.11.2024  
Date of finished: 06.12.2024 

## Лабораторная работа №3 "Развертывание Netbox, сеть связи как источник правды в системе технического учета Netbox"

<b>Описание:</b> в данной лабораторной работе вы ознакомитесь с интеграцией Ansible и Netbox и изучите методы сбора информации с помощью данной интеграции.
<p><b>Цель работы:</b> c помощью Ansible и Netbox собрать всю возможную информацию об устройствах и сохранить их в отдельном файле.</p>

### Ход работы
#### Установка Netbox

Поднимем Netbox в Yandex Cloud. При установке использовала [эту статью] (https://jtprog.ru/netbox/) как гайд.

1. Установим PostgreSQL
> Совет от чемпиона по ошибкам: не забудьте выполнить команду ```sudo apt-get update``` перед установкой, сохраните час своей жизни.

<img src="img/psqlins.png" width="500">

2. Подключимся к PostgreSQL.

<img src="img/connect.png" width="500">


3. Создадим базу данных и пользователя netbox, зададим смешной пароль. !!!не забудем поставить точку с запятой в конце каждой строки.

<img src="img/createb.png" width="500">

4. Проверим, что БД доступна для подключения.Ура, реально доступна.

<img src="img/lolkek.png" width="500">

5. Установим Redis и поиграем с ним в пингпонг *радостно понгает*.

<img src="img/pong.png" width="500">

6. Переходим к установка и настройке NetBox. Перед этим установим все нужные пакеты. Создадим отдельную директорию для NetBox и склонируем туда репозиторий NetBox.

Внимание! Сюжетный твист. На этапе клонирования репозитория (да, пришлось установить гит) стали появляться ошибки (моя любимая с sudo) и я случайно нашла статью про установку netbox через docker. Cветлая мысль пришла мне в голову. Хочу всё сделать через Docker, нажатием одной кнопки. Спойлер: понадобилось двое суток.

7. Чудесная [статья](https://adsm.readthedocs.io/ru/latest/3_ipam_dcim/installation.html) с видео + [гитхаб](https://github.com/netbox-community/netbox-docker?tab=readme-ov-file) netbox 

8. Возникшие проблемы: пришлось вручную обновить версию docker compose до последней, проверять зависимости и смотреть логи контейнера netbox-1, потому что он заболел.

<img src="img/unhealthy.png" width="500">

9. Фиксируем момент, когда всё хорошо(помогла перезагрузка YC).
```
git clone -b release https://github.com/netbox-community/netbox-docker.git
cd netbox-docker
tee docker-compose.override.yml <<EOF
services:
  netbox:
    ports:
      - 8000:8080
EOF
docker compose pull
docker compose up
```

Проверим контейнеры
<img src="img/ps.png" width="500">

Убедимся, что стоит нужная версия docker-compose
<img src="img/vers.png" width="500">

Запустим процесс
<img src="img/compose.png" width="500">

Победа! Примерно через 15 минут мы получаем доступ к приложению в веб-браузере
<img src="img/enter.png" width="500">

Создаем суперпользователя пользователя netbox ```docker compose exec netbox /opt/netbox/netbox/manage.py createsuperuser``` и заходим.





<p><b>Вывод:</b> в ходе выполнения данной лабораторной работе мы ознакомитесь с интеграцией Ansible и Netbox и изучили методы сбора информации с помощью данной интеграции.</p>
