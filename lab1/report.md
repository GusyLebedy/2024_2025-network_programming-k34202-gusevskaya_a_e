University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34202  
Author: Gusevskaya Arina  
Lab: Lab1  
Date of create: 25.09.2024  
Date of finished: 26.09.2024 

## Лабораторная работа №1 "Установка CHR и Ansible, настройка VPN"

<b>Описание:</b> данная работа предусматривает обучение развертыванию виртуальных машин (VM) и системы контроля конфигураций Ansible а также организации собственных VPN серверов.
<p><b>Цель работы:</b> развернуть виртуальную машину на базе платформы GCP с установленной системой контроля конфигураций Ansible и установить CHR в VirtualBox.</p>

### Ход работы
#### Создание ВМ на GCP
1. Создадим ВМ в Google Cloud на образе Ubuntu 20.04

![alt text](img/create.png)

2. Проверим конфигурацию и корректность работы ВМ.

![alt text](img/conf.png)

3. Установим python3 и ansible. Убедимся, что всё установлено корректно.
P.S. В команде проверки `ls -la /usr/bin/python3*` используем символ *, потому что мы не знаем, какая конкретно версия установлена и не можем её указать.

![alt text](img/install.png)

#### Установка CHR (RouterOS) на VirtualBox
1. Создадим ВМ с помощью Oracle VirtualBox с образом, который был скачат с сайта mikrotik.com

<img src="img/monitor.png" width="500">

2. Узнаем ip-адресс СНR

![alt text](img/ip.png)

<details>
<summary> Возникшие трудности </summary>



</details>



### Результаты лабораторной работы




### Вывод
В ходе лабораторной работы была развернута виртуальная машина на базе платформы GCP с установленной системой контроля конфигураций Ansible, а также CHR в VirtualBox. Между ними был настроен WireGuard туннель.
