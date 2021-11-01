# Домашнее задание к занятию "3.1. Работа в терминале, лекция 1"

1. Установите средство виртуализации [Oracle VirtualBox](https://www.virtualbox.org/).+

1. Установите средство автоматизации [Hashicorp Vagrant](https://www.vagrantup.com/).+

1. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал. Можно предложить:

	* iTerm2 в Mac OS X
	* Windows Terminal в Windows
	* выбрать цветовую схему, размер окна, шрифтов и т.д.
	* почитать о кастомизации PS1/применить при желании.

	Несколько популярных проблем:
	* Добавьте Vagrant в правила исключения перехватывающих трафик для анализа антивирусов, таких как Kaspersky, если у вас возникают связанные с SSL/TLS ошибки,
	* MobaXterm может конфликтовать с Vagrant в Windows,
	* Vagrant плохо работает с директориями с кириллицей (может быть вашей домашней директорией), тогда можно либо изменить [VAGRANT_HOME](https://www.vagrantup.com/docs/other/environmental-variables#vagrant_home), либо создать в системе профиль пользователя с английским именем,
	* VirtualBox конфликтует с Windows Hyper-V и его необходимо [отключить](https://www.vagrantup.com/docs/installation#windows-virtualbox-and-hyper-v),
	* [WSL2](https://docs.microsoft.com/ru-ru/windows/wsl/wsl2-faq#does-wsl-2-use-hyper-v-will-it-be-available-on-windows-10-home) использует Hyper-V, поэтому с ним VirtualBox также несовместим,
	* аппаратная виртуализация (Intel VT-x, AMD-V) должна быть активна в BIOS,
	* в Linux при установке [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads) может дополнительно потребоваться пакет `linux-headers-generic` (debian-based) / `kernel-devel` (rhel-based).

1. С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant:

	* Создайте директорию, в которой будут храниться конфигурационные файлы Vagrant. В ней выполните `vagrant init`. Замените содержимое Vagrantfile по умолчанию следующим:

		```bash
		Vagrant.configure("2") do |config|
			config.vm.box = "bento/ubuntu-20.04"
		end
		```

	* Выполнение в этой директории `vagrant up` установит провайдер VirtualBox для Vagrant, скачает необходимый образ и запустит виртуальную машину.

	* `vagrant suspend` выключит виртуальную машину с сохранением ее состояния (т.е., при следующем `vagrant up` будут запущены все процессы внутри, которые работали на момент вызова suspend), `vagrant halt` выключит виртуальную машину штатным образом.

1. Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?

![image](https://user-images.githubusercontent.com/91008731/137999175-860dcf71-beb7-4b05-b2dd-92b1527f1051.png)


1. Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: [документация](https://www.vagrantup.com/docs/providers/virtualbox/configuration.html). Как добавить оперативной памяти или ресурсов процессора виртуальной машине?

config.vm.provider "virtualbox" do |v|
  v.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
end


1. Команда `vagrant ssh` из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.

1. Ознакомиться с разделами `man bash`, почитать о настройках самого bash:
    * какой переменной можно задать длину журнала `history`, и на какой строчке manual это описывается?

![history](https://user-images.githubusercontent.com/91008731/139276711-308d8a67-c7dc-4f06-b11a-fb87401796db.JPG)

    * что делает директива `ignoreboth` в bash?
    ![image](https://user-images.githubusercontent.com/91008731/139277863-ea4853b9-9d13-4a8f-bd87-2328379bb454.png)

1. В каких сценариях использования применимы скобки `{}` и на какой строчке `man bash` это описано?
![image](https://user-images.githubusercontent.com/91008731/139278777-e82be760-7eaf-4748-8adb-a82d61e7ea24.png)


3. С учётом ответа на предыдущий вопрос, как создать однократным вызовом `touch` 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?

получится,  используя фигурные скобки. vagrant@vagrant:~$ touch test {1..100000}

![image](https://user-images.githubusercontent.com/91008731/139695658-834f4220-9cb0-4733-ad61-b9729da45624.png)
нет,  не получится создать.  список аргументов слишком длинный. 

11. В man bash поищите по `/\[\[`. Что делает конструкция `[[ -d /tmp ]]`

выаолняется условие ![image](https://user-images.githubusercontent.com/91008731/139283825-3469da63-a95d-4cd3-84cd-87551022cfb6.png) 

![image](https://user-images.githubusercontent.com/91008731/139697039-e0141df2-3e3f-4717-9502-dd37cec24a9b.png)
![image](https://user-images.githubusercontent.com/91008731/139697143-94f075a0-a157-42ab-a59b-ff700e5affd7.png)
файл существует в следующей директории. Проверяет файл и директорию. [[  ]] альтернатива команде тест.

7. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:

	```bash
	bash is /tmp/new_path_directory/bash
	bash is /usr/local/bin/bash
	bash is /bin/bash
	```

	(прочие строки могут отличаться содержимым и порядком)
    В качестве ответа приведите команды, которые позволили вам добиться указанного вывода или соответствующие скриншоты.
    
    ![image](https://user-images.githubusercontent.com/91008731/139291779-2be3c2cc-a224-461c-a3c6-0fcf99dcb6ad.png)


1. Чем отличается планирование команд с помощью `batch` и `at`?
![image](https://user-images.githubusercontent.com/91008731/139292057-f91b7242-f025-4233-bee0-fb1add5d5aa9.png)



1. Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.

 
 ---

