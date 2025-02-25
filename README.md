# Никоноров Денис - FOPS-8
# Домашнее задание к занятию «Управление доступом»

------

### Задание 1.Создайте конфигурацию для подключения пользователя
1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для подключения.
3. Создайте роли и все необходимые настройки для пользователя.
4. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (`kubectl logs pod <pod_id>`, `kubectl describe pod <pod_id>`).
5. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

------

### Решение задания 1.Создайте конфигурацию для подключения пользователя

Использую OpenSSL создал файл ключа.
И создан запрос на подписание сертификата (CSR)

![alt text](img/1.png)

Генерация файла сертивиката (CRT). Используя ключи кластера положить их по пути `/var/snap/microk8s/current/certs/`:

![alt text](img/2.png)

Настройка конфигурационого файла kubectl для подключения

Создан пользователь `staff` и настраиваю его на использование созданого ключа выше.

![alt text](img/3.png)

Создан новый контекст с именем `staff-context` и подключен к пользователю `staff`.

![alt text](img/4.png)

Проверка контекста

![alt text](img/5.png)

Он создался.

Создание отдельного namespace:

![alt text](img/6_1.png)
![alt text](img/6_2.png)

Включаю встроеный в Microk8s RBAC контроллера.

![alt text](img/7.png)

Написан манифест [role](/role.yml) который создаст (Role) и манифест [rolebinfing](/rolebinding.yml) привязывает роль к namespace.

![alt text](img/8_1.png)
![alt text](img/8_2.png)
![alt text](img/8_3.png)

Что бы проверить права пользователя надо перелючится в его контекст.

![alt text](img/9.png)

Разворачиваю [deployment](/deployment.yml) в разрешенном для пользователя Namespace:

![alt text](img/10.png)

Теперь проверим какие развернуты поды в namespace с именем `default`:

![alt text](img/11.png)

Видно что в namespace с именем `default` нет доступа из-за того что он не был указан в манифесте Role.

Но проверив поды в namespace `access-control`, то список подов будет выведен.

![alt text](img/12.png)

Проверка логов пода

![alt text](img/13.png)

Проверка describe пода

![alt text](img/14.png)

Манифест роли и ее привязка к namespace, у пользователя `stuff` есть доступ к подам, логам и описанию