

1.Создаем домен и прописываем DNS
![image](https://user-images.githubusercontent.com/91008731/189588264-7e147679-2836-4975-9273-eae7a46487d6.png)

2. Создаем конфигурацию для terraform в github. Ссылка на конфигурационные файлы terraform: https://github.com/Wasjusja/terraform.git

- В provider.tf добавим конфигурацию провайдера yandex:

terraform {
  required_providers {
    yandex = {
      source  = "yandex-cloud/yandex"
      version = "0.75.0"
    }
  }
}

provider "yandex" {
  token     = var.YANDEX_TOKEN
  cloud_id  = var.yandex_cloud_id
  folder_id = var.yandex_folder_id
  zone      = "ru-central1-a"
}


 - В variables.tf добавим Яндекс токен и идентефикаторы облака:

variable "YANDEX_TOKEN" {}

variable "yandex_folder_id" {
  default = "b1glcs1c44jip4ce4jcb"
}

variable "yandex_cloud_id" {
  default = "b1g5jatgvp7evt4p5s99"
}

variable "SSH_ID_RSA_PUB" {}


 - В main.tf добавим конфигурацию backend:

terraform {
  cloud {
    organization = "zagovorov"

    workspaces {
      name = "devops-diplom-yandexcloud"
    }
  }
}


 - В main.tf добавим настройки всех инстансов

-  В nextwork.tf добавим сетевые настройки.
-  В output.tf добавим вывод настроек доступа и IP-адресов.


3. Запускаем terraform на terraform cloud

Запуск Terraform
Выполним terraform init:
terraform init


Initializing Terraform Cloud...

Initializing provider plugins...
- Finding yandex-cloud/yandex versions matching "0.75.0"...
- Installing yandex-cloud/yandex v0.75.0...
- Installed yandex-cloud/yandex v0.75.0 (self-signed, key ID E40F590B50BB8E40)

...

Terraform Cloud has been successfully initialized!

You may now begin working with Terraform Cloud. Try running "terraform plan" to
see any changes that are required for your infrastructure.

If you ever set or change modules or Terraform Settings, run "terraform init"
again to reinitialize your working directory.

Выполним terraform validate:

terraform validate

Success! The configuration is valid.

Добавим переменные окружения:
TF_VAR_YANDEX_TOKEN 
TF_VAR_SSH_ID_RSA_PUB — копируем из cat ~/.ssh/id_rsa.pub.

Выполним terraform plan:

terraform apply --auto-approve

Выполним terraform apply --auto-approve.
terraform apply --auto-approve

проверяем,  что получилось

![image](https://user-images.githubusercontent.com/91008731/189588527-8d9d7999-1247-47ec-99a1-cfc8e6a28c9d.png)

4. Проверяем,  как машинки запустились на yandex cloud

![image](https://user-images.githubusercontent.com/91008731/189745940-b0fbb8d0-523a-426d-9906-79ec1d371e04.png)

5. Смотрим зоны DNS

![image](https://user-images.githubusercontent.com/91008731/189747371-95d6252d-eb3f-4309-8f61-9072b1f26f23.png)


Ansible
Подготовим роли Ansible для созданной инфраструктуры. Файлы конфигурации https://github.com/Wasjusja/devops-netology/tree/main/devops-diplom-yandexcloud/ansible.

Описание:

В roles/common добавим сценарии для установки Docker, Docker Compose и Node exporter.
В roles/entrance добавим роль для работы основного сервера.
В roles/mysql добавим роль для работы кластера MySQL.
В roles/wordpress добавим роль для работы WordPress.
В roles/monitoring добавим роль для работы Prometheus, Alert Manager и Grafana.

Для работы Ansible добавим IP-адреса:

Добавим значение output-переменной Terraform в ~/.ssh/config:

Host zagovoroff.ru
  HostName 51.250.13.198
  User ubuntu
  IdentityFile ~/.ssh/id_rsa
  
Host db01.zagovoroff.ru
  HostName 10.100.1.25
  User ubuntu
  IdentityFile ~/.ssh/id_rsa
    ProxyJump ubuntu@51.250.13.198
    ProxyCommand ssh -W %h:%p -i .ssh/id_rsa

Host db02.zagovoroff.ru
  HostName 10.100.1.23
  User ubuntu
  IdentityFile ~/.ssh/id_rsa
    ProxyJump ubuntu@51.250.13.198
    ProxyCommand ssh -W %h:%p -i .ssh/id_rsa

Host app.zagovoroff.ru
  HostName 10.100.1.7
  User ubuntu
  IdentityFile ~/.ssh/id_rsa
    ProxyJump ubuntu@51.250.13.198
    ProxyCommand ssh -W %h:%p -i .ssh/id_rsa

Host monitoring.zagovoroff.ru
  HostName 10.100.1.5
  User ubuntu
  IdentityFile ~/.ssh/id_rsa
    ProxyJump ubuntu@51.250.13.198
    ProxyCommand ssh -W %h:%p -i .ssh/id_rsa

Host gitlab.zagovoroff.ru
  HostName 10.100.1.4
  User ubuntu
  IdentityFile ~/.ssh/id_rsa
    ProxyJump ubuntu@51.250.13.198
    ProxyCommand ssh -W %h:%p -i .ssh/id_rsa

Очистим файл known_hosts командой echo "" > ~/.ssh/known_hosts

Добавим IP-адреса серверов в variables.yml:

app_zagovoroff_ru_ip_addr_internal: "10.100.1.7"
db01_zagovoroff_ru_ip_addr_internal: "10.100.1.25"
db02_zagovoroff_ru_ip_addr_internal: "10.100.1.23"
entrance_zagovoroff_ru_ip_addr_external: "51.250.13.198"
gitlab_zagovoroff_ru_ip_addr_internal: "10.100.1.4"
monitoring_zagovoroff_ru_ip_addr_internal: "10.100.1.5"
runner_zagovoroff_ru_ip_addr_internal: "10.100.1.19"


Добавим соответствующие IP-адреса в конфигурацию Prometheus:
  - job_name: 'entrance-node'
    scrape_interval: 10s
    static_configs:
      - targets: [ '51.250.13.198' ]
      
  - job_name: 'gitlab-node'
    scrape_interval: 10s
    static_configs:
      - targets: [ '10.100.1.4:9100' ]
  
  - job_name: 'monitoring-node'
    scrape_interval: 10s
    static_configs:
      - targets: [ '10.100.1.5:9100' ]
                  
  - job_name: 'wordpress-node'
    scrape_interval: 10s
    static_configs:
      - targets: [ '10.100.1.7:9100' ]

  - job_name: 'db01-node'
    scrape_interval: 10s
    static_configs:
      - targets: [ '10.100.1.25:9100' ]

  - job_name: 'db02-node'
    scrape_interval: 10s
    static_configs:
      - targets: [ '10.100.1.23:9100' ]

Запускаем ansible

Запустим роль для основного сервера:

ansible-playbook roles/entrance/tasks/main.yml

...
PLAY RECAP ***********************************************************************************************************************************************************************************************
zagovoroff.ru              : ok=28   changed=25   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

Запускаем роль кластера MySQL:
ansible-playbook db/tasks/main.yml

...
PLAY RECAP ***********************************************************************************************************************************************************************************************
db01.zagovoroff.ru         : ok=27   changed=21   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
db02.zagovoroff.ru         : ok=22   changed=18   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


Запустим роль для WordPress:


ansible-playbook app/tasks/main.yml 

...
PLAY RECAP ***********************************************************************************************************************************************************************************************
app.zagovoroff.ru          : ok=26   changed=23   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


<img width="1680" alt="wp-install (1)" src="https://user-images.githubusercontent.com/91008731/190279322-fc357bf3-aee1-4864-9939-18373f5914da.png">



Запускаем роль для Prometheus, Alert Manager и Grafana:
ansible-playbook monitoring/tasks/main.yml 

...
PLAY RECAP ***********************************************************************************************************************************************************************************************
monitoring.zagovoroff.ru   : ok=15   changed=13   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  

<img width="1680" alt="grafana-dashboard" src="https://user-images.githubusercontent.com/91008731/190279200-867a35a3-7cf3-4186-a6a3-4801de4a3ee0.png">

