# Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"

1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?

windows
![image](https://user-images.githubusercontent.com/91008731/143221701-32feec32-cc33-45d4-b0d1-c769bc86a33f.png)
ipconfig /all

linux
ifconfig -a
![image](https://user-images.githubusercontent.com/91008731/143223770-6abe2ec0-36ba-4843-b2b8-8a04317176ad.png)

ip link show

![image](https://user-images.githubusercontent.com/91008731/143224328-ef0d8460-baa0-4765-ba58-b87ed2bba418.png)
ip addr

ls /sys/class/net
![image](https://user-images.githubusercontent.com/91008731/143224643-c55f21c5-9901-428b-af12-ca6abd1826aa.png)

cat /proc/net/dev

netstat -i

![image](https://user-images.githubusercontent.com/91008731/143225118-ae08bda7-a894-45e5-81a0-bfd29dd30550.png)




2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?

протокод lldp
и пакет lldp

![image](https://user-images.githubusercontent.com/91008731/143227951-d6167b21-b3f9-4c93-8780-284473e34387.png)


3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
Применяется технология виртуального разделения коммутатора VLAN, утилита vconfig

![image](https://user-images.githubusercontent.com/91008731/143231542-bae47571-ab96-40ae-be6e-ddbaf6f70b77.png)




4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.

bonding
Mode-0(balance-rr) по умолчанию
Balance-rr пакеты отправляются по кругу от первого к последнему
Mode-1(active-backup) – Один из интерфейсов работает в активном режиме, остальные в ожидающем
Mode-2(balance-xor) 
Mode-3(broadcast) – Происходит передача во все объединенные интерфейсы, тем самым обеспечивая отказоустойчивость.
Mode-4(802.3ad) – динамическое объединение одинаковых портов. В данном режиме можно значительно увеличить пропускную способность входящего так и исходящего трафика.
Mode-5(balance-tlb) – Адаптивная балансировки нагрузки трафика. Входящий трафик получается только активным интерфейсом, исходящий распределяется в зависимости от текущей загрузки канала каждого интерфейса.

Mode-6(balance-alb) 

The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto usb0
allow-hotplug usb0
iface usb0 inet dhcp

auto bond0

iface bond0 inet static
address 192.168.0.1
netmask 255.255.255.0
network 192.168.0.0

slaves eth0 eth1
bond-mode balance-rr

5. Сколько IP адресов в сети с маской /29 ? 8 IP
 Сколько /29 подсетей можно получить из сети с маской /24. 
![image](https://user-images.githubusercontent.com/91008731/143242554-aa0734bd-662c-4383-9259-0290db1d35f0.png)
![image](https://user-images.githubusercontent.com/91008731/143242853-c77b0591-3746-4c25-ad90-b6293b64df45.png)


 Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
![image](https://user-images.githubusercontent.com/91008731/143242962-0e9cabde-5fdb-4b54-ae80-a208a74f6b07.png)


6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.

100.64.0.0
![image](https://user-images.githubusercontent.com/91008731/143244824-5b9f4eb5-6060-4a3e-9863-10d5e152848f.png)


7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?

в windows

![image](https://user-images.githubusercontent.com/91008731/143252669-722f9bd2-9afe-44ed-9610-96368b04a315.png)

в lnux

![image](https://user-images.githubusercontent.com/91008731/143253265-98d381a9-f5b3-4bef-86aa-82703fbfd067.png)
sudo ip -s -s neigh flush all

sudo arp -d 192.168.0.102


 -
---
