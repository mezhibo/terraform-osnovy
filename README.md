**Задание 0**

1) Ознакомьтесь с документацией к security-groups в Yandex Cloud. Этот функционал понадобится к следующей лекции.


**Решение 0**

Ознакомился с документация по [security groups](https://yandex.cloud/ru/docs/vpc/concepts/security-groups?from=int-console-help-center-or-nav)


**Задание 1**

В качестве ответа всегда полностью прикладывайте ваш terraform-код в git. Убедитесь что ваша версия Terraform ~>1.8.4

1. Изучите проект. В файле variables.tf объявлены переменные для Yandex provider.

2. Создайте сервисный аккаунт и ключ. service_account_key_file.

3. Сгенерируйте новый или используйте свой текущий ssh-ключ. Запишите его открытую(public) часть в переменную vms_ssh_public_root_key.

4. Инициализируйте проект, выполните код. Исправьте намеренно допущенные синтаксические ошибки. Ищите внимательно, посимвольно. Ответьте, в чём заключается их суть.

5. Подключитесь к консоли ВМ через ssh и выполните команду  curl ifconfig.me. Примечание: К OS ubuntu "out of a box, те из коробки" необходимо подключаться под пользователем ubuntu: "ssh ubuntu@vm_ip_address". Предварительно убедитесь, что ваш ключ добавлен в ssh-агент: eval $(ssh-agent) && ssh-add Вы познакомитесь с тем как при создании ВМ создать своего пользователя в блоке metadata в следующей лекции.;

6. Ответьте, как в процессе обучения могут пригодиться параметры preemptible = true и core_fraction=5 в параметрах ВМ.


В качестве решения приложите:

- скриншот ЛК Yandex Cloud с созданной ВМ, где видно внешний ip-адрес;

- скриншот консоли, curl должен отобразить тот же внешний ip-адрес;

- ответы на вопросы.



**Решение 1**

Проверим актуальность версии Terraform

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/f538dd7c2225a9cf7156c099d3cb08c452159a0d/IMG/1.jpg)


Создаем одноименный сервисный аккаунт

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/f538dd7c2225a9cf7156c099d3cb08c452159a0d/IMG/2.jpg)



Делаем инициализацию проекта 

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/f538dd7c2225a9cf7156c099d3cb08c452159a0d/IMG/3.jpg)


Так как у нас сгенерирован json фаил для сервисного аккаунта, то для подключения вводить токен уже нет необходимости, то комментируем эту строку с переменной токена

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/f538dd7c2225a9cf7156c099d3cb08c452159a0d/IMG/4.jpg)


Пара ключей давно использовалась, менять ничего не стал, просто указал путь и публичный ключ для создаваемой машины в переменных

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/f538dd7c2225a9cf7156c099d3cb08c452159a0d/IMG/5.jpg)


Прописываем ключ в метадату 

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/5e048bbef5f612d225b1adf9cf065ea4bcc416bc/IMG/6.jpg)


Далее исправим ошибки в коде, а именно:

- Ошибка в platform_id: "standart-v4" Исправлено на: "standard-v3"(синтаксическа ошибка тоже исправлена) , так как standard-v3 не даст использовать 5% CPU. Поэтому либо standard-v1, либо standard-v2, standard-v3 не подходит, а standard-v4 не существует. Либо же просто увеличим максимальный процент CPU для этого задания, далее буду создавать в standard-v1 либо standard-v2 для использования 5% CPU

- Неверное написание идентификатора платформы. standard вместо standart. (как замечено выше)

- И изменим значения ресурсов на минимально допустимые, 1-ядро недопустимо, делаем 2 ядра, RAM оставим 1Гб, и 5% использования CPU изменим под standard-v3 и зададим 20%

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/dd1bd8c0a0b336ef6cfba6931fe74e3ab49b7197/IMG/14.jpg)


Теперь запускаем Terraform apply, создаем машину, и идем смотреть ее ip на веб-интрефейсе


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/086b6663107045d12ad127203e713cc0afff5d92/IMG/7.jpg)



Копируем ip-адрес и подлючаемся по ssh, по дефолту будет использован ключ для пользователя ubuntu


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/086b6663107045d12ad127203e713cc0afff5d92/IMG/8.jpg)


Параметры preemptible = true и core_fraction=5 в процессе обучения нужны для экономии средств на балансе.

- preemptible = true означает что машина прерываемая, и что гарантировано будет остановлена в любое время в течении 24 часов - что сущесвтенно снижает стоимость такой машины

- core_fraction=5 означает

```
Гарантированная доля vCPU, которая будет выделена ВМ. ВМ с гарантированной долей меньше 100% обеспечивают указанный уровень производительности с вероятностью временного повышения вплоть до 100%. Такие ВМ подходят для задач, которые не требуют постоянной гарантии производительности vCPU на 100%.
 
```

**Задание 2**

1. Замените все хардкод-значения для ресурсов yandex_compute_image и yandex_compute_instance на отдельные переменные. К названиям переменных ВМ добавьте в начало префикс vm_web_ . Пример: vm_web_name.

2. Объявите нужные переменные в файле variables.tf, обязательно указывайте тип переменной. Заполните их default прежними значениями из main.tf.

3. Проверьте terraform plan. Изменений быть не должно.


**Решение 2**


Вынесем все хардкод-значения для ресурсов yandex_compute_image и yandex_compute_instance в отдельные переменные в файл variables.tf


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/b9bc2bc5599506482fc6301392c56921bbfcd109/IMG/15.jpg)


Теперь заполним файл main.tf новыми значениями, но уже с переменными


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/237f47ee3f6a2db0edab5e04c85f2b22ad5d7182/IMG/16.jpg)



Сделаем terraform plan 

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/237f47ee3f6a2db0edab5e04c85f2b22ad5d7182/IMG/17.jpg)


Изменений нет. Значит все верно.



**Задание 3**

1. Создайте в корне проекта файл 'vms_platform.tf' . Перенесите в него все переменные первой ВМ.

2. Скопируйте блок ресурса и создайте с его помощью вторую ВМ в файле main.tf: "netology-develop-platform-db" , cores  = 2, memory = 2, core_fraction = 20. Объявите её переменные с префиксом vm_db_ в том же файле ('vms_platform.tf'). ВМ должна работать в зоне "ru-central1-b"

3. Примените изменения.


**Решение 3**


Создадим файл vms_platform.tf с описанием переменных для второй машины


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/5fead830d5232110ba86b11c850c5fce53b7c85b/IMG/22.jpg)


В файле main.tf опишем ресурс и subnet для него созданными ранее переменными

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/5fead830d5232110ba86b11c850c5fce53b7c85b/IMG/23.jpg)


Применим terraform apply

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/5fead830d5232110ba86b11c850c5fce53b7c85b/IMG/20.jpg)

И посмотрим на результат в веб-интерфейсе


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/5fead830d5232110ba86b11c850c5fce53b7c85b/IMG/21.jpg)



**Задание 4**

1. Объявите в файле outputs.tf один output , содержащий: instance_name, external_ip, fqdn для каждой из ВМ в удобном лично для вас формате.(без хардкода!!!)

2. Примените изменения.

В качестве решения приложите вывод значений ip-адресов команды terraform output.


**Решение 4**

Запишем в ранее пустой файл outputs.tf выражения для вывода данных о созданных машинах, и применим.

После terraform apply видими результат работы output 

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/020c5bc2f77eed30a3297cf85c696c56b63404c8/IMG/24.jpg)




**Задание 5**

1. В файле locals.tf опишите в одном local-блоке имя каждой ВМ, используйте интерполяцию ${..} с НЕСКОЛЬКИМИ переменными по примеру из лекции.

2. Замените переменные внутри ресурса ВМ на созданные вами local-переменные.

3. Примените изменения.


**Решение 5**

Закомментируем старые значения в файле variables.tf и впишем новые для формирования locals


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/3764c93b1d6ede20df4295cecdcf7e6e6fdc2f9c/IMG/25.jpg)


Опишем locals переменные 

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/3764c93b1d6ede20df4295cecdcf7e6e6fdc2f9c/IMG/26.jpg)


И теперь изменим name на наше значение из locals

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/3764c93b1d6ede20df4295cecdcf7e6e6fdc2f9c/IMG/27.jpg)

Пересоздадим машины, и все ГУД

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/706bef12814cc7b0ab28a33050489c0ff1f217dc/IMG/29.jpg)



**Задание 6**


1. Вместо использования трёх переменных ".._cores",".._memory",".._core_fraction" в блоке resources {...}, объедините их в единую map-переменную vms_resources и внутри неё конфиги обеих ВМ в виде вложенного map(object).

```

пример из terraform.tfvars:
vms_resources = {
  web={
    cores=2
    memory=2
    core_fraction=5
    hdd_size=10
    hdd_type="network-hdd"
    ...
  },
  db= {
    cores=2
    memory=4
    core_fraction=20
    hdd_size=10
    hdd_type="network-ssd"
    ...
  }
}

```

Создайте и используйте отдельную map(object) переменную для блока metadata, она должна быть общая для всех ваших ВМ.

```

пример из terraform.tfvars:
metadata = {
  serial-port-enable = 1
  ssh-keys           = "ubuntu:ssh-ed25519 AAAAC..."
}

```

Найдите и закоментируйте все, более не используемые переменные проекта.

Проверьте terraform plan. Изменений быть не должно.





