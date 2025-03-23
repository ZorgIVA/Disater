# Домашнее задание к занятию 1 «Disaster recovery и Keepalived»

# Задание 1

[Схема](https://github.com/ZorgIVA/Disater/blob/master/img/hsrp.pkt)

Дана схема для Cisco Packet Tracer, рассматриваемая в лекции.
На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

<img src = "img\1_1.jpg" width = 100%>

<img src = "img\1_2.jpg" width = 100%>

# Задание 2
Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного файла.
Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

Bash-скрипт: [check.sh](https://github.com/ZorgIVA/Disater/blob/master/img/check.sh)

#!/bin/bash
if [[ $(netstat -ant | grep LISTEN | grep :80) ]] && [[ -f /var/www/html/index.nginx-debian.html ]]; then
  exit 0
else
  sudo systemctl stop keepalived
fi


**MASTER** [keepalived11.conf](https://github.com/ZorgIVA/Disater/blob/master/img/keepalived11.conf)

<!--vrrp_script check {
        script "/home/iva/check.sh"
        interval 3
}

vrrp_instance VI_1 {
        state MASTER
        interface enp0s3
        virtual_router_id 15
        priority 255
        advert_int 1

        virtual_ipaddress {
                192.168.123.99/24
        }

        track_script {
                check
        }

}-->

**BACKUP** [keepalived22.conf](https://github.com/ZorgIVA/Disater/blob/master/img/keepalived22.conf)

