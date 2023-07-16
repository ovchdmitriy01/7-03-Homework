# Домашнее задание к занятию «Подъём инфраструктуры в Yandex Cloud»

### Оформление домашнего задания

1. Домашнее задание выполните в [Google Docs](https://docs.google.com/) и отправьте на проверку ссылку на ваш документ в личном кабинете.  
1. В названии файла укажите номер лекции и фамилию студента. Пример названия: 7.3. Подъём инфраструктуры в Yandex Cloud — Александр Александров.
1. Перед отправкой проверьте, что доступ для просмотра открыт всем, у кого есть ссылка. Если нужно прикрепить дополнительные ссылки, добавьте их в свой Google Docs.

Любые вопросы по решению задач задавайте в чате учебной группы.

 ---

### Задание 1 

### Ответ

Установка ВМ с Ubuntu 22.04. Содержимое файла конфигурации main.tf 


```

terraform {
  required_providers {
    yandex = {
      source  = "yandex-cloud/yandex"
    }
  }
}
 
provider "yandex" {
  token     = "y0_AgAAAAAndjpyAATuwQAAAADlw3mSak3GeDegRB6GDj831S3d2IIiInA"
  cloud_id  = "b1ga46o1a1d4tohv7tdr"
  folder_id = "b1gds4vik45llv3oh05l"
  zone      = "ru-central1-b"
}

data "yandex_compute_image" "ubuntu_image" {
  family = "ubuntu-2204-lts"
}
 
resource "yandex_compute_instance" "vm-1" {
  name = "terraform1"

  resources {
    cores  = 2
    memory = 2
  }

  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu_image.id
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.subnet-1.id
    nat       = true
  }
  
  metadata = {
    user-data = "${file("./meta.txt")}"
  }
}
resource "yandex_vpc_network" "network-1" {
  name = "network1"
}

resource "yandex_vpc_subnet" "subnet-1" {
  name           = "subnet1"
  zone           = "ru-central1-b"
  network_id     = yandex_vpc_network.network-1.id
  v4_cidr_blocks = ["192.168.10.0/24"]
}

output "internal_ip_address_vm_1" {
  value = yandex_compute_instance.vm-1.network_interface.0.ip_address
}
output "external_ip_address_vm_1" {
  value = yandex_compute_instance.vm-1.network_interface.0.nat_ip_address
}

```


  Содержимое файла meta.txt


 ```
    
  #cloud-config
users:
- name: ubu1
  groups: sudo
  shell: /bin/bash
  sudo: ['ALL=(ALL) NOPASSWD:ALL']
  ssh-authorized-keys:
    - ssh-rsa AAAAB...hCBEFoNQqT2RA0E= ubu1@ubu1
      
 ```

Проверка конфинурации.
  
`terraform validate`
 
<a href="https://ibb.co/PFWnNtL"><img src="https://i.ibb.co/PFWnNtL/7-3-1.png" alt="7-3-1" border="0"></a>

`terraform plan`

<a href="https://ibb.co/MDk4Gpn"><img src="https://i.ibb.co/MDk4Gpn/7-3-2.png" alt="7-3-2" border="0"></a>
<a href="https://ibb.co/XtghVgX"><img src="https://i.ibb.co/XtghVgX/7-3-3.png" alt="7-3-3" border="0"></a>
 
 Установка ВМ `terraform applay`
 
<a href="https://ibb.co/dW11dDk"><img src="https://i.ibb.co/dW11dDk/7-3-4.png" alt="7-3-4" border="0"></a>
<a href="https://ibb.co/fN7Y7MY"><img src="https://i.ibb.co/fN7Y7MY/7-3-5.png" alt="7-3-5" border="0"></a>

 
Содержимое файла `playbook-nginx.yml`

```

- name: Instal nginx
  hosts: 51.250.98.197
  become: true

  tasks:
  - name: Install nginx
    apt:
      name: nginx
      state: latest

  - name: Start nginx
    systemd:
      name: nginx
      enabled: true
      state: started
    notify:
      - nginx systemd

```

Установка nginx и проверка nginx

<a href="https://ibb.co/16r9YZF"><img src="https://i.ibb.co/16r9YZF/7-3-6.png" alt="7-3-6" border="0"></a>
<a href="https://ibb.co/d7w7V5Q"><img src="https://i.ibb.co/d7w7V5Q/7-3-7.png" alt="7-3-7" border="0"></a>
<a href="https://ibb.co/wYxVV5h"><img src="https://i.ibb.co/wYxVV5h/7-3-8.png" alt="7-3-8" border="0"></a>

---

