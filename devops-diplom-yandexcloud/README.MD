

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



Рекомендуемый вариант: самостоятельная установка Kubernetes кластера.
а. При помощи Terraform подготовить как минимум 3 виртуальных машины Compute Cloud для создания Kubernetes-кластера. Тип виртуальной машины следует выбрать самостоятельно с учётом требовании к производительности и стоимости. Если в дальнейшем поймете, что необходимо сменить тип инстанса, используйте Terraform для внесения изменений.
б. Подготовить ansible конфигурации, можно воспользоваться, например Kubespray
в. Задеплоить Kubernetes на подготовленные ранее инстансы, в случае нехватки каких-либо ресурсов вы всегда можете создать их при помощи Terraform.
Альтернативный вариант: воспользуйтесь сервисом Yandex Managed Service for Kubernetes
а. С помощью terraform resource для kubernetes создать региональный мастер kubernetes с размещением нод в разных 3 подсетях
б. С помощью terraform resource для kubernetes node group
Ожидаемый результат:

Работоспособный Kubernetes кластер.
В файле ~/.kube/config находятся данные для доступа к кластеру.
Команда kubectl get pods --all-namespaces отрабатывает без ошибок.
Создание тестового приложения
Для перехода к следующему этапу необходимо подготовить тестовое приложение, эмулирующее основное приложение разрабатываемое вашей компанией.

Способ подготовки:

Рекомендуемый вариант:
а. Создайте отдельный git репозиторий с простым nginx конфигом, который будет отдавать статические данные.
б. Подготовьте Dockerfile для создания образа приложения.
Альтернативный вариант:
а. Используйте любой другой код, главное, чтобы был самостоятельно создан Dockerfile.
Ожидаемый результат:

Git репозиторий с тестовым приложением и Dockerfile.
Регистр с собранным docker image. В качестве регистра может быть DockerHub или Yandex Container Registry, созданный также с помощью terraform.
Подготовка cистемы мониторинга и деплой приложения
Уже должны быть готовы конфигурации для автоматического создания облачной инфраструктуры и поднятия Kubernetes кластера.
Теперь необходимо подготовить конфигурационные файлы для настройки нашего Kubernetes кластера.

Цель:

Задеплоить в кластер prometheus, grafana, alertmanager, экспортер основных метрик Kubernetes.
Задеплоить тестовое приложение, например, nginx сервер отдающий статическую страницу.
Рекомендуемый способ выполнения:

Воспользовать пакетом kube-prometheus, который уже включает в себя Kubernetes оператор для grafana, prometheus, alertmanager и node_exporter. При желании можете собрать все эти приложения отдельно.
Для организации конфигурации использовать qbec, основанный на jsonnet. Обратите внимание на имеющиеся функции для интеграции helm конфигов и helm charts
Если на первом этапе вы не воспользовались Terraform Cloud, то задеплойте в кластер atlantis для отслеживания изменений инфраструктуры.
Альтернативный вариант:

Для организации конфигурации можно использовать helm charts
Ожидаемый результат:

Git репозиторий с конфигурационными файлами для настройки Kubernetes.
Http доступ к web интерфейсу grafana.
Дашборды в grafana отображающие состояние Kubernetes кластера.
Http доступ к тестовому приложению.
Установка и настройка CI/CD
Осталось настроить ci/cd систему для автоматической сборки docker image и деплоя приложения при изменении кода.

Цель:

Автоматическая сборка docker образа при коммите в репозиторий с тестовым приложением.
Автоматический деплой нового docker образа.
Можно использовать teamcity, jenkins либо gitlab ci

Ожидаемый результат:

Интерфейс ci/cd сервиса доступен по http.
При любом коммите в репозиторие с тестовым приложением происходит сборка и отправка в регистр Docker образа.
При создании тега (например, v1.0.0) происходит сборка и отправка с соответствующим label в регистр, а также деплой соответствующего Docker образа в кластер Kubernetes.
