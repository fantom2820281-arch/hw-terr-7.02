
## Домашняя работа terrafom-7.02

---


Студент:   ###  Герасин Дмитрий Сергеевич  ###

    Курс: Netology, CI/CD
    Дата: 19.02.2026







📜 Информация о проекте

    Версия Terraform: v1.15.0-alpha20260204
    Провайдеры: 
        kreuzwerker/docker ~>3.0
        hashicorp/random v3.8.1
        hashicorp/local  v2.7.0

        структура проекта   

├── main.tf         
├── provider.tf      
├── README.md
└── variables.tfform.io/*/*"




        обязательно создаем файл .terraormrc  информация для терраформ где скачивать провайдера.

```hcl

provider_installation {
  network_mirror {
    url = "https://terraform-mirror.yandexcloud.net/"
    include = ["registry.terraform.io/*/*"]
  }
  direct {
    exclude = ["registry.terraform.io/*/*"]
  }
}
```

### 🐳 Terraform Docker Lab

   doker Version:    29.2.1

Локальное развёртывание веб-стека через Terraform: Nginx (reverse proxy) + WordPress.

![Terraform](https://img.shields.io/badge/Terraform-v1.15-blue?logo=terraform)
![Docker](https://img.shields.io/badge/Docker-Engine-blue?logo=docker)

---


terraform init

 3. Просмотр плана развёртывания

terraform plan

 Применение конфигурации
terraform apply 
         

---

Задание
Установите Terraform на компьютерную систему (виртуальную или хостовую), используя лекцию или инструкцию. download: https://hashicorp-releases.yandexcloud.net/terraform/
В связи с недоступностью ресурсов для загрузки Terraform на территории РФ, вы можете использовать зеркало из репозитория по ссылке.


---

 терраформ установился корректно 


 terraform --version
Terraform v1.15.0-alpha20260204
on linux_amd64
+ provider registry.terraform.io/hashicorp/local v2.7.0
+ provider registry.terraform.io/hashicorp/random v3.8.1
+ provider registry.terraform.io/kreuzwerker/docker v3.0.2


---

задание 2 

Повторите демо из лекции! документация провайдеров: https://library.tf/providers

В рамках выполнения домашнего задания созданы все необходимые файлы 

.gitignore

```
# Terraform
.terraform/
terraform.tfstate
terraform.tfstate.*
terraform.tfvars
*.tfvars
*.tfstate
*.tfstate.backup

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Logs
*.log


# Temporary files
tmp/
*.tmp
```
main.tf 

```hcl
#docker rm -f $(docker ps -a -q)
#terraform console
#curl -L  http://127.0.0.1:1080 ; curl -L  http://127.0.0.1:2080

resource "docker_image" "nginx" {
  name         = "nginx:1.21.1" # или var.containers.nginx.image
  keep_locally = true
}

resource "docker_image" "wordpress" {
  name         = var.containers.wordpress.image
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "our-cool-project-${var.containers.nginx.name}" #interpolation

  ports {
    internal = var.containers.nginx.ports.internal
    external = var.containers.nginx.ports.external
  }
  network_mode = "bridge"
}

resource "docker_container" "wordpress" {
  image = docker_image.wordpress.image_id
  name  = "our-cool-project-${var.containers.wordpress.name}" #interpolation

  ports {
    internal = var.containers.wordpress.ports.internal
    external = var.containers.wordpress.ports.external
  }
  network_mode = "bridge"
}

resource "random_password" "any_uniq_name" {
  length = 16
}

resource "local_file" "xxx" {
  content  = "our-cool-project-${random_password.any_uniq_name.result}"
  filename = "/tmp/xxx.txt"
}

data "local_file" "from_resourse" {

  filename = "/etc/passwd"
}
```
provider.tf


```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "3.8.1"
    }
    local = {
      source  = "hashicorp/local"
      version = "2.7.0"
    }
  }
}

provider "docker" {
  host = "unix:///var/run/docker.sock"
}
```
variables.tf

```variable "string" {
  type    = string
  default = "some string"
}
variable "number" {
  type    = number
  default = 1
}
variable "list_of_strings" {
  type    = list(string)
  default = ["a", "b", "c"]
}
variable "list_of_numbers" {
  type    = list(number)
  default = [1, 2, 3]
}

variable "bool" {
  type    = bool
  default = true
}

variable "map" {
  type = map(string)
  default = {
    name       = "John"
    surname    = "Connor"
    try_number = 10
  }
}

variable "containers" {
  type = map(object({
    name  = string
    image = string
    ports = object({
      external = number
      internal = number
    })
  }))
  default = {
    nginx = {
      name  = "reverse-proxy-nginx"
      image = "nginx:1.21.1"
      ports = {
        internal = 80
        external = 1080
      }
    },
    wordpress = {
      name  = "web-wordpress"
      image = "wordpress:latest"
      ports = {
        internal = 80
        external = 2080
      }
    }
  }
}
```


и выполняем terraform init , terraforn plan

Plan: 6 to add, 0 to change, 0 to destroy.

---

ресурсы создалоись успешно

---


[scrin1](hw-terr1.png)   

---

terraform apply

---

[scrin2](hw-terr2.png)

---


заходим в браузер проверяем 

curl http://localhost:1080    # Nginx reverse proxy
curl http://localhost:2080    # WordPress

---

[scrin3](hw-terr3.png)

---

### Важно перед запуском terraform apply обновить все компоненты системы 

---
---
---

