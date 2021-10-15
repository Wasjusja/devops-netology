Вопрос 1
Находим хэш  по начальному названию с помощью git show aefea
commit aefead2207ef7e2aa5dc81a34aedf0cad4c32545
Update CHANGELOG.md

Вопрос 2
commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)

Вопрос 3
У коммита 2 родетеля
$ git show b8d720~
commit 56cd7859e05c36c06b56d013b55a252d0bb7e158
Merge: 58dcac4b7 ffbcf5581

Вопрос 4
$ git log v0.12.23..v0.12.24
commit 33ff1c03bb960b332be3af2e333462dde88b279e (tag: v0.12.24)
Author: tf-release-bot <terraform@hashicorp.com>
Date:   Thu Mar 19 15:04:05 2020 +0000

    v0.12.24

commit b14b74c4939dcab573326f4e3ee2a62e23e12f89
Author: Chris Griggs <cgriggs@hashicorp.com>
Date:   Tue Mar 10 08:59:20 2020 -0700

    [Website] vmc provider links

commit 3f235065b9347a758efadc92295b540ee0a5e26e
Author: Alisdair McDiarmid <alisdair@users.noreply.github.com>
Date:   Thu Mar 19 10:39:31 2020 -0400

    Update CHANGELOG.md

commit 6ae64e247b332925b872447e9ce869657281c2bf
Author: Alisdair McDiarmid <alisdair@users.noreply.github.com>
Date:   Thu Mar 19 10:20:10 2020 -0400

    registry: Fix panic when server is unreachable

вопрос 5
 b9a93a0fe70f115bcceee802101e129aab81cd21
сначала ищем функцию при помощи git grep и потом ищем коммит при помощи  git log -s provider_source.go

вопрос 6

 $ git log -S globalPluginDirs --oneline
35a058fb3 main: configure credentials from the CLI config file
c0b176109 prevent log output during init
8364383c3 Push plugin discovery down into command package


Вопрос 7
 git grep -L synchronizedWriters
Находим файлы с этой функцией и потом автора git blame
Daniel Dreier 



