# Настройка PXE сервера для автоматической установки.

Цель:

Отрабатка навыков установки и настройки DHCP, TFTP, PXE загрузчика и автоматической загрузки.

1) Установить и настроить загрузку по сети для дистрибутива CentOS8.
2) Поменять установку из репозитория NFS на установку из репозитория HTTP.
3) Настроить автоматическую установку для созданного kickstart файла.

## Развертывание стенда для демострации работы PXE сервера.

Стэнд состоит из хостовой машины под управлением ОС Ubuntu 20.04 на которой развернуты Vagrant и Ansible и двух виртуальных машин `pxeserver` с установленной на него bento/centos-8.4 и `pxeclient`, на него собственно и будет производиться установка ОС.

Разворачивание инфраструктуры необходимо производить поэтапно, а именно сначала запусить и настроить `pxeserver`, а уже потом запускать `pxeclient`, иначе `pxeclient` не даст нормально завершиться инструкциям `Vagrantfile`, так как будет ожидать установку по LAN, которая не возможна, так как сам `pxeserver` еще не настроен.

Ссылку скачиывания дистрибутива `CentOS 8.4.2105` поменял, так как ссылка из методички была недоступна.

Разворачиваем инфраструктуру в `Vagrant` исключительно через `Ansible`.

Все коментарии по каждому блоку указаны в тексте `Playbook` - `pxe.yml`.


Выполняем установку `pxeserver`

```
vagrant up pxeserver
```
## Результат работы

Сначала проверяем работу WEB серврера



После запускаем `pxeclient`

```
vagrant up pxeclient
```
и смотрим вывод меню загрузки


выбираем загрузку по умолчанию --> `Auto-install CentOS 8.4`



начинается процесс установки в автоматическом режиме


установка завершена, необходима перезагрузка.