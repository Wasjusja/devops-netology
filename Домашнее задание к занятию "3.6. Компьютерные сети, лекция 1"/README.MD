# Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"

1. Работа c HTTP через телнет.
- Подключитесь утилитой телнет к сайту stackoverflow.com
`telnet stackoverflow.com 80`

![image](https://user-images.githubusercontent.com/91008731/142673959-445082bb-1785-4f64-926b-4cd6f4b34a5c.png)



- отправьте HTTP запрос
```bash
GET /questions HTTP/1.0
HOST: stackoverflow.com
[press enter]
[press enter]
```
![image](https://user-images.githubusercontent.com/91008731/142676610-2353036a-91ad-4929-9925-d6004e03db69.png)
стандартный код ответа HTTP, получаемый в ответ от сервера в ситуации, когда запрошенный ресурс был на постоянной основе перемещён, new location  https://stackoverflow.com/

- В ответе укажите полученный HTTP код, что он означает?



2. Повторите задание 1 в браузере, используя консоль разработчика F12.

- откройте вкладку `Network`
- отправьте запрос http://stackoverflow.com
- найдите первый ответ HTTP сервера, откройте вкладку `Headers`
- укажите в ответе полученный HTTP код.


![image](https://user-images.githubusercontent.com/91008731/142679300-6fa7cc29-ac45-46f5-a635-5fb4fe3ded89.png)

- проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?
- приложите скриншот консоли браузера в ответ.
![image](https://user-images.githubusercontent.com/91008731/142679637-21036f1c-28d8-40de-858a-a36ac7ffd8a1.png)
загрузка контента


4. Какой IP адрес у вас в интернете?
![image](https://user-images.githubusercontent.com/91008731/142679970-a6d113e3-47cf-48ec-9928-b1dadc490944.png)


5. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой `whois`

![image](https://user-images.githubusercontent.com/91008731/142681269-6ffb823e-d511-4f44-8709-f53d891092ae.png)

![image](https://user-images.githubusercontent.com/91008731/142681381-65fb7b61-54ed-4d9a-b655-f2c03734d1c9.png)

6. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой `traceroute`
Пакет проходит через шлюз, роутер и локальную сеть и дальше идет в сеть AS SkyNet, AS Google 

![image](https://user-images.githubusercontent.com/91008731/142801858-d19567c4-5750-4b32-99f3-bb04ec50ba85.png)




7. Повторите задание 5 в утилите `mtr`. На каком участке наибольшая задержка - delay?

![image](https://user-images.githubusercontent.com/91008731/142802404-91bab41c-8458-40c1-8313-c46071c080e2.png)
Самый большой delay на последних 2 хостах

11. 216.239.48.163                                                                                                                0.0%   158   41.0  16.0   8.2  77.8  10.4
12. 209.85.246.111                                                                                                                0.0%   158   15.5  14.2   9.2  56.8   5.2


7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой `dig`
![image](https://user-images.githubusercontent.com/91008731/143202335-753917c9-fe3a-4680-836d-86372acd0db7.png)


8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой `dig`
![image](https://user-images.githubusercontent.com/91008731/143208872-7b796cd7-8cfb-4b67-a3d4-30385ec1b457.png)
lt-in-f102.1e100.net.
lt-in-f113.1e100.net
lt-in-f138.1e100.net.

доменное имя 1e100.net






---
