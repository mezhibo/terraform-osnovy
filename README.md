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

- Ошибка в platform_id: "standart-v4" Исправлено на: "standard-v1"

- Неверное написание идентификатора платформы. standard вместо standart.

- И изменим значения ресурсов на минимально допустимые, 1-ядро и 5% использования ЦП изменим на 20%, так как в веб-интерфйесе среди рекомендованных указано 20%, хотя можно и 5%. Сделаю для себя 20% ради теста для первого задания, потом в конце изменю на 5%

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/086b6663107045d12ad127203e713cc0afff5d92/IMG/9.jpg)


Теперь запускаем Terraform apply, создаем машину, и идем смотреть ее ip на веб-интрефейсе


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/086b6663107045d12ad127203e713cc0afff5d92/IMG/7.jpg)



Копируем ip-адрес и подлючаемся по ssh, по дефолту будет использован ключ для пользователя ubuntu


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/086b6663107045d12ad127203e713cc0afff5d92/IMG/8.jpg)


Параметры preemptible = true и core_fraction=5 в процессе обучения нужны для экономии средств на балансе.

- preemptible = true означает что машина прерываемая, и что гарантировано будет остановлена в любое время в течении 24 часов - что сущесвтенно снижает стоимость такой машины

- core_fraction=5 слишком маленькое значение, будем использовать core_fraction=20, т.к через веб интерфейс нам рекомендуют использовать именно его, хотя можем оставить и 5 )


![alt text](https://github.com/mezhibo/terraform-osnovy/blob/941ea39cab9705650bf2801bc508e37f0d9006b9/IMG/10.jpg)

Это у нас гарантированная доля CPU, для себя решил 20%, по совету веб-интерфейса, хотя никто не мешал установить 5.

![alt text](https://github.com/mezhibo/terraform-osnovy/blob/95f269a0fcd948e116c0731acf90e7b39af37b63/IMG/12.jpg)



**Задание 2**

1. Замените все хардкод-значения для ресурсов yandex_compute_image и yandex_compute_instance на отдельные переменные. К названиям переменных ВМ добавьте в начало префикс vm_web_ . Пример: vm_web_name.

2. Объявите нужные переменные в файле variables.tf, обязательно указывайте тип переменной. Заполните их default прежними значениями из main.tf.

3. Проверьте terraform plan. Изменений быть не должно.


**Решение 2**














