# Домашнее задание к занятию "`ZABBIX ч. 2`" - `Никифоров Роман`


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

Настройка HSRP

![Items](https://github.com/thrsnknwldgthtsntpwr/git-hw/blob/main/img/img1.png)

---

### Задание 2

Плавающий IP на eth0 (Keepalived)
![Items](https://github.com/thrsnknwldgthtsntpwr/git-hw/blob/main/img/img2.png)
Скрипт проверки 80-ого порта и наличия файла /var/www/html/index.html

```
#!/bin/sh
RESULT=1
nc -z localhost 80
a=$?
test -f /var/www/html/index.html
b=$?

if [ $a -eq 0 ] && [ $b -eq 0 ]
then
 exit 0
else
 exit 1
fi
```

keepalived.conf


```

vrrp_script check_www {
        script       "/etc/keepalived/check.sh"
        interval 3   # check every 2 seconds
        fall 1       # require 2 failures for KO
        rise 1       # require 2 successes for OK
}

vrrp_instance VI_1 {
        state MASTER
        interface eth0
        virtual_router_id 65
        priority 255
        advert_int 1

        virtual_ipaddress {
              192.168.129.65/24
        }
        track_script {
                check_www
        }
}

```

Демонстрация работы (80-ый порт открыт/закрыт)

![Items](https://github.com/thrsnknwldgthtsntpwr/git-hw/blob/main/img/img3.png)

---

