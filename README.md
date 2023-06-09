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
вывод хода проигрывания `Playbook` - `pxe.yml` на `pxeserver`

```
root@root-ubuntu:/home/roman/project# ansible-playbook -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory pxe.yml

PLAY [Set up PXE Server] **********************************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************************************
ok: [pxeserver]

TASK [set up repo] ****************************************************************************************************************************************************************************************
changed: [pxeserver] => (item=/etc/yum.repos.d/CentOS-Linux-AppStream.repo)
changed: [pxeserver] => (item=/etc/yum.repos.d/CentOS-Linux-BaseOS.repo)

TASK [set up repo] ****************************************************************************************************************************************************************************************
ok: [pxeserver] => (item=/etc/yum.repos.d/CentOS-Linux-AppStream.repo)
ok: [pxeserver] => (item=/etc/yum.repos.d/CentOS-Linux-BaseOS.repo)

TASK [install softs on CentOS] ****************************************************************************************************************************************************************************
ok: [pxeserver]

TASK [Download ISO image CentOS 8.4.2105] *****************************************************************************************************************************************************************
ok: [pxeserver]

TASK [Create ISO directory] *******************************************************************************************************************************************************************************
ok: [pxeserver]

TASK [Mount ISO image] ************************************************************************************************************************************************************************************
ok: [pxeserver]

TASK [copy ALL files from /mnt to /iso] *******************************************************************************************************************************************************************
ok: [pxeserver]

TASK [set up httpd config] ********************************************************************************************************************************************************************************
changed: [pxeserver]

TASK [restart httpd] **************************************************************************************************************************************************************************************
changed: [pxeserver]

TASK [Create TFTP directory] ******************************************************************************************************************************************************************************
ok: [pxeserver]

TASK [set up pxelinux] ************************************************************************************************************************************************************************************
changed: [pxeserver]

TASK [extract packages syslinux] **************************************************************************************************************************************************************************
changed: [pxeserver]

TASK [copy files to TFTP share] ***************************************************************************************************************************************************************************
ok: [pxeserver] => (item=pxelinux.0)
ok: [pxeserver] => (item=ldlinux.c32)
ok: [pxeserver] => (item=libmenu.c32)
ok: [pxeserver] => (item=libutil.c32)
ok: [pxeserver] => (item=menu.c32)
ok: [pxeserver] => (item=vesamenu.c32)

TASK [copy initrd and vmlinuz files to TFTP share] ********************************************************************************************************************************************************
ok: [pxeserver] => (item=initrd.img)
ok: [pxeserver] => (item=vmlinuz)

TASK [restart tftp-server] ********************************************************************************************************************************************************************************
changed: [pxeserver]

TASK [set up dhcp-server] *********************************************************************************************************************************************************************************
ok: [pxeserver]

TASK [restart dhcp-server] ********************************************************************************************************************************************************************************
changed: [pxeserver]

TASK [copy ks.cfg] ****************************************************************************************************************************************************************************************
changed: [pxeserver]

PLAY RECAP ************************************************************************************************************************************************************************************************
pxeserver                  : ok=19   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

## Результат работы

Сначала проверяем работу WEB серврера

![WEB](https://user-images.githubusercontent.com/114483769/235338978-e18218eb-81da-4c0d-a498-d61a8b82fa33.png)


После запускаем `pxeclient`

```
vagrant up pxeclient
```
и смотрим вывод меню загрузки

![VB-загрузка](https://user-images.githubusercontent.com/114483769/235338985-79164fa8-52cc-42c2-83f3-6e4a6d58e653.png)

выбираем загрузку по умолчанию --> `Auto-install CentOS 8.4`

![kickstart_wait](https://user-images.githubusercontent.com/114483769/235338998-61a2a277-71d2-4911-a82d-c17f7563d238.png)

начинается процесс установки в автоматическом режиме

![kickstart_complete](https://user-images.githubusercontent.com/114483769/235339004-730edf4a-5245-4544-919f-548d0782f78c.png)

установка завершена, необходима перезагрузка.
