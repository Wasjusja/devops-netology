# Домашнее задание к занятию "3.3. Операционные системы, лекция 1"

1. Какой системный вызов делает команда `cd`? В прошлом ДЗ мы выяснили, что `cd` не является самостоятельной  программой, это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Тем не менее, вы можете запустить `strace` на `/bin/bash -c 'cd /tmp'`. В этом случае вы увидите полный список системных вызовов, которые делает сам `bash` при старте. Вам нужно найти тот единственный, который относится именно к `cd`. Обратите внимание, что `strace` выдаёт результат своей работы в поток stderr, а не в stdout.

системный вызов: chdir("/tmp")   

![image](https://user-images.githubusercontent.com/91008731/139861331-ac262abf-526e-42e9-aac1-1e854cf1886d.png)


2. Попробуйте использовать команду `file` на объекты разных типов на файловой системе. Например:
    ```bash
    vagrant@netology1:~$ file /dev/tty       openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
    /dev/tty: character special (5/0)
    
    vagrant@netology1:~$ file /dev/sda        openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libmagic.so.1", O_RDONLY|O_CLOEXEC) = 3

    /dev/sda: block special (8/0)
   
   vagrant@netology1:~$ file /bin/bash    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libmagic.so.1", O_RDONLY|O_CLOEXEC) = 3

    /bin/bash: ELF 64-bit LSB shared object, x86-64
    ```
    Используя `strace` выясните, где находится база данных `file` на основании которой она делает свои догадки.
. 
3.Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

Находим файл  lsof | grep deleted
![image](https://user-images.githubusercontent.com/91008731/139904776-505f2d47-670d-4f77-869a-681a9d4a3e4c.png)
![image](https://user-images.githubusercontent.com/91008731/139909629-8f61aee9-ae2b-463e-b506-0287645a82d9.png)

cat /dev/null > /proc/15692/fd/3

 
4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?

Зомби -процессы не занимают ресурсы системы,  запись остаетсся лишь в таблице. Это процесс завершенный, только родитель забыл забрать код возврата.
5. В iovisor BCC есть утилита `opensnoop`:
    ```bash
    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
    /usr/sbin/opensnoop-bpfcc
    ```
    На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools` для Ubuntu 20.04. Дополнительные [сведения по установке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).
![image](https://user-images.githubusercontent.com/91008731/139915771-6484a372-2d6e-4ed4-817f-4ae660faf348.png)
Вызовы на демоны ядра и демоны шины 
 

6. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС.
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3 системный вызов uname. 
![image](https://user-images.githubusercontent.com/91008731/139919481-3155a547-89a7-44f9-86f5-8095aa8843e6.png)
Linux vagrant 5.4.0-80-generic #90-Ubuntu SMP Fri Jul 9 22:49:44 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
![image](https://user-images.githubusercontent.com/91008731/139919990-5e8d8f6f-b677-45a6-be36-8a9ff55a4f6a.png)


7. Чем отличается последовательность команд через `;` и через `&&` в bash? Например:
    ```bash
    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#
    ```
    Есть ли смысл использовать в bash `&&`, если применить `set -e`?
    
    ![image](https://user-images.githubusercontent.com/91008731/140088041-2c205a20-2fa4-4cfb-910c-b44597b97015.png)

    ![image](https://user-images.githubusercontent.com/91008731/140088387-58e69bac-5dd2-414f-b8fd-cc3f429f49f7.png)
; выполняет команды последовательно, && 2 командв выпонится при условии что 1 выполнена.

Есть ли смысл использовать в bash `&&`, если применить `set -e`?

Наверное,  нет, так как обе команды прекрящают дальнейшее выполнение при ошибки. 

set -e останавливает выолнение команды при ошибке, то есть возврашении всего кроме 0. 
    
8. Из каких опций состоит режим bash `set -euxo pipefail` и почему его хорошо было бы использовать в сценариях?

![image](https://user-images.githubusercontent.com/91008731/140096525-2882ce7f-771d-4a72-aeae-7bb0b74c2596.png)
set -e, -u, -x, -o pipefail

![image](https://user-images.githubusercontent.com/91008731/140133388-723b6cd2-ea2b-46b6-ba28-68a648398881.png)
![image](https://user-images.githubusercontent.com/91008731/140133909-491d8dd5-c3eb-4931-a320-b67030ab2795.png)
![image](https://user-images.githubusercontent.com/91008731/140134601-a500cb32-fbeb-4fc6-ab92-64514096e925.png)
![image](https://user-images.githubusercontent.com/91008731/140135155-f91af5a4-7d39-4431-814e-9179b20ddb79.png)
То есть идеально для отладки сценариев и скриптов,  показывает выполнени всех команд и ошибки , если есть и прекращает сценарий при ошибке.

9. Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе. В `man ps` ознакомьтесь (`/PROCESS STATE CODES`) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).
![image](https://user-images.githubusercontent.com/91008731/140162935-07611f0b-af17-45a9-bc55-a525b168e989.png)
самые частотные  I    Idle kernel thread
S    interruptible sleep (waiting for an event to complete)
 ![image](https://user-images.githubusercontent.com/91008731/140165079-f888b4c2-225b-4b6b-89dc-ee7519ea5380.png)

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
