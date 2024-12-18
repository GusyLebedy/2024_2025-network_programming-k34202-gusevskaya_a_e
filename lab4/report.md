University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34202  
Author: Gusevskaya Arina  
Lab: Lab4  
Date of create: 18.12.2024  
Date of finished: 20.12.2024

## Лабораторная работа №4 "Базовая 'коммутация' и туннелирование используя язык программирования P4"

### Описание
В данной лабораторной работе будет осуществлено знакомство на практике с языком программирования P4, разработанный компанией Barefoot (ныне Intel) для организации процесса обработки сетевого трафика на скорости чипа. Barefoot разработал несколько FPGA чипов для обработки трафика которые были встроенны в некоторые модели коммутаторов Arista и Brocade.

### Цель работы
Изучить синтаксис языка программирования P4 и выполнить 2 задания обучающих задания от Open network foundation для ознакомления на практике с P4.

### Ход работы

## Создание виртуальной машины 
1. Vagrant был установлен ранее в ходе выполнения работы по другому предмету. Версия представлена на рисунке. Отмечу, что для установки и скачивания дополнительных пакетов необходимо включить VPN.

![alt text](img/vagrant.png)

2. Скачаем шаблон конфигурации `P4 Tutorial` и импортируем в VirtualBox.
3. Запустим ВМ

![alt text](img/wall.png)

## Реализация базовой переадресации

1. Зайдем в папку задания и выполним команду ```make run```, которая скомпилирует basic.p4 (изначальный вариант).
  
2. После запуска Mininet убедимся, что пинг не поддреживается. Для этого поднимем `mininet`

![alt text](img/ping.png)

3. Внесем в файл `basic.p4` изменения, которые обозначены в задании:
   
> В раздел Parsers добавим парсинг `ethernet` и `ipv4 headers`. Парсер — это функция, которая отображает пакеты в заголовках и метаданные, написанные на языке конечного автомата.

```c
/*************************************************************************
*********************** P A R S E R  ***********************************
*************************************************************************/

parser MyParser(packet_in packet,
                out headers hdr,
                inout metadata meta,
                inout standard_metadata_t standard_metadata) {

    state start {
        transition parse_ethernet;
    }

    state parse_ethernet {
        packet.extract(hdr.ethernet);
        transition select(hdr.ethernet.etherType) {
            TYPE_IPV4 : parse_ipv4;
            default : accept;
        }
    }

    state parse_ipv4 {
        packet.extract(hdr.ipv4);
        transition accept;
    }
}
```

> В раздел Ingress processing добавим метод, который отвечает за пересылки пакетов и устанавливает выходный порт, обновляет MAC адрес назначения, обновляет исходный MAC адрес, уменьшает значение TTL. Пропишем условие проверки правильности заголвка ipv4.

```c
/*************************************************************************
**************  I N G R E S S   P R O C E S S I N G   *******************
*************************************************************************/

control MyIngress(inout headers hdr,
                  inout metadata meta,
                  inout standard_metadata_t standard_metadata) {
    action drop() {
        mark_to_drop(standard_metadata);
    }

    action ipv4_forward(macAddr_t dstAddr, egressSpec_t port) {
        standard_metadata.egress_spec = port;
        hdr.ethernet.srcAddr = hdr.ethernet.dstAddr;
        hdr.ethernet.dstAddr = dstAddr;
        hdr.ipv4.ttl = hdr.ipv4.ttl - 1;
    }

    table ipv4_lpm {
        key = {
            hdr.ipv4.dstAddr: lpm;
        }
        actions = {
            ipv4_forward;
            drop;
            NoAction;
        }
        size = 1024;
        default_action = NoAction();
    }

    apply {
        if (hdr.ipv4.isValid()) {
            ipv4_lpm.apply();
        }
    }
}
```

> В разделе Deparsers добавим депарсинг `ethernet` и `ipv4 headers`.

```c
/*************************************************************************
***********************  D E P A R S E R  *******************************
*************************************************************************/

control MyDeparser(packet_out packet, in headers hdr) {
    apply {
        packet.emit(hdr.ethernet);
        packet.emit(hdr.ipv4);
    }
}
```

