# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

    * поместите его в автозагрузку,
    * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
    * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
Node Exporter установлен и работает. 

Опции передаются через unit file

![image](https://user-images.githubusercontent.com/91008731/148079713-9d2077f2-7c3e-43a1-8e4f-bde59dda2eae.png)

![image](https://user-images.githubusercontent.com/91008731/142164263-479cff25-ecda-4bff-ba77-fbcec030d037.png)
Данные и метрики выводит
![image](https://user-images.githubusercontent.com/91008731/142169772-704ae9d3-6617-4703-b941-bd86f44c9e2a.png)



2 Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
![image](https://user-images.githubusercontent.com/91008731/142169772-704ae9d3-6617-4703-b941-bd86f44c9e2a.png)

3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
    * в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
    * добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:
Netdata уствновлена,  порты проброшены.
![image](https://user-images.githubusercontent.com/91008731/148061413-8d50306e-495c-4c10-9c0a-0b33173d5e17.png)


    После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
    
    ![image](https://user-images.githubusercontent.com/91008731/148061531-a42e6eee-11cc-4753-baa9-ef56dc70ca96.png)
Собираются такие метрики как CPU utilization по ядрам, system Load, Disk monitoring, memory, networking stack

![image](https://user-images.githubusercontent.com/91008731/148062630-f5523f59-6876-457f-9897-77d2243f39dd.png)
![image](https://user-images.githubusercontent.com/91008731/148062715-f7e02e58-b723-46da-96a5-08e48c213b08.png)
![image](https://user-images.githubusercontent.com/91008731/148062878-79e2dd84-e2ce-4e73-ac49-4e5af5697edf.png)

3. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
осознает

![image](https://user-images.githubusercontent.com/91008731/142196466-dd2ba57f-e35f-423d-b3f0-d7c25cf98f44.png)
![i![image](https://user-images.githubusercontent.com/91008731/142196979-54bef127-98ab-4c9c-aaa2-dfec531b35b4.png)
![image](https://user-images.githubusercontent.com/91008731/142196999-4dc08c8f-077c-46eb-a7c7-ffeb14cfab65.png)


4. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?

Данный параметр обозначает лимит на открытие дескрипторов ядра (системы), крвтное 1024
![image](https://user-images.githubusercontent.com/91008731/142211733-7cb6f311-5c87-4e56-8a9f-19ae9ee58c02.png)
cat /proc/sys/fs/nr_open
![image](https://user-images.githubusercontent.com/91008731/142216083-f8ff03d8-651e-4ba5-a0ec-c8bbce8bcd57.png)
unlimit -n c с помощью этого параметра можно менять число открытых дискрипторов,  но не больше чем максимальное для системы. 

6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.
![image](https://user-images.githubusercontent.com/91008731/142219307-8e1d7430-4ba2-40f1-9f7a-026ab5c897bf.png)

заходим под root sudo -i

запукаем sleep через unshare
![image](https://user-images.githubusercontent.com/91008731/148070976-5b072cfd-88a8-4733-8b08-fc3f14f37cdf.png)

заходим в процес
![image](https://user-images.githubusercontent.com/91008731/148071405-7947fec9-764c-4cae-99f7-68d55b167fe0.png)



7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?

![image](https://user-images.githubusercontent.com/91008731/148071956-e83882eb-e7f2-4bc7-947a-08db45ef81eb.png)

![image](https://user-images.githubusercontent.com/91008731/148073826-d9cf3977-5490-49bf-bd93-8c1046410888.png)
Через dmesg смотрим , сработал механизм контейнерезации контрольных групп,  чтобы сберечб ресурсы. 

По умолчанию параметры можно посмотреть здесь

ulimit -a 
![image](https://user-images.githubusercontent.com/91008731/148075915-6eec2fc6-c621-4aa2-9e73-83203df3ec7c.png)

Изменить настройки можно здесь

/etc/security/limits.conf или /etc/limits.conf

# /etc/security/limits.conf
#
#Each line describes a limit for a user in the form:
#
#<domain>        <type>  <item>  <value>
#
#Where:
#<domain> can be:
#        - a user name
#        - a group name, with @group syntax
#        - the wildcard *, for default entry
#        - the wildcard %, can be also used with %group syntax,
#                 for maxlogin limit
#        - NOTE: group and wildcard limits are not applied to root.
#          To apply a limit to the root user, <domain> must be
#          the literal username root.
#
#<type> can have the two values:
#        - "soft" for enforcing the soft limits
#        - "hard" for enforcing hard limits
#
#<item> can be one of the following:
#        - core - limits the core file size (KB)
#        - data - max data size (KB)
#        - fsize - maximum filesize (KB)
#        - memlock - max locked-in-memory address space (KB)
#        - nofile - max number of open file descriptors
#        - rss - max resident set size (KB)
#        - stack - max stack size (KB)
#        - cpu - max CPU time (MIN)
#        - nproc - max number of processes
#        - as - address space limit (KB)
#        - maxlogins - max number of logins for this user
#        - maxsyslogins - max number of logins on the system
#        - priority - the priority to run user process with
#        - locks - max number of file locks the user can hold
#        - sigpending - max number of pending signals
#        - msgqueue - max memory used by POSIX message queues (bytes)
#        - nice - max nice priority allowed to raise to values: [-20, 19]
#        - rtprio - max realtime priority
#        - chroot - change root to directory (Debian-specific)
#
#<domain>      <type>  <item>         <value>
#

#*               soft    core            0
#root            hard    core            100000
#*               hard    rss             10000
#@student        hard    nproc           20
#@faculty        soft    nproc           20
#@faculty        hard    nproc           50
#ftp             hard    nproc           0
#ftp             -       chroot          /ftp
#@student        -       maxlogins       4

# End of file



 
 ---

## Как сдавать задания

Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.

Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

Домашнее задание выполните в файле readme.md в github репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

Также вы можете выполнить задание в [Google Docs](https://docs.google.com/document/u/0/?tgif=d) и отправить в личном кабинете на проверку ссылку на ваш документ.
Название файла Google Docs должно содержать номер лекции и фамилию студента. Пример названия: "1.1. Введение в DevOps — Сусанна Алиева".

Если необходимо прикрепить дополнительные ссылки, просто добавьте их в свой Google Docs.

Перед тем как выслать ссылку, убедитесь, что ее содержимое не является приватным (открыто на комментирование всем, у кого есть ссылка), иначе преподаватель не сможет проверить работу. Чтобы это проверить, откройте ссылку в браузере в режиме инкогнито.

[Как предоставить доступ к файлам и папкам на Google Диске](https://support.google.com/docs/answer/2494822?hl=ru&co=GENIE.Platform%3DDesktop)

[Как запустить chrome в режиме инкогнито ](https://support.google.com/chrome/answer/95464?co=GENIE.Platform%3DDesktop&hl=ru)

[Как запустить  Safari в режиме инкогнито ](https://support.apple.com/ru-ru/guide/safari/ibrw1069/mac)

Любые вопросы по решению задач задавайте в чате Slack.

---
