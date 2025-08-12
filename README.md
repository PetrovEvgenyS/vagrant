# Vagrantfile для VirtualBox

## Описание
Данный Vagrantfile поднимает несколько виртуальных машин с использованием VirtualBox.  
Каждая ВМ получает:
- Фиксированное имя и hostname.
- Статический IP в вашей сети.
- Параметры CPU, RAM и сетевого bridge, указанные в конфигурации.
- Отключённую папку `/vagrant`.

## Требования
- **Vagrant**
- **VirtualBox**
- Рабочий сетевой адаптер в режиме bridge (должен совпадать с `HOST_BRIDGE` в Vagrantfile).  
- Локально установленный box `almalinux-9.3` (или свой).  

## Подготовка бокса
Если box локальный:
```powershell
vagrant box add almalinux-9.3 file:///C:/DiskD/Vagrant/boxs/almalinux/box1.box
```

Проверка:
```powershell
vagrant box list
```

## Настройка сети
В `HOST_BRIDGE` укажите точное имя адаптера из вывода:
```powershell
VBoxManage list bridgedifs
```

IP-адреса (`10.100.10.11`, `10.100.10.12`, `10.100.10.13`) должны быть свободны в вашей сети.

## Конфигурация
В начале файла определены ключевые параметры:
```ruby
HOST_SHOW_GUI  = false   # GUI VirtualBox
HOST_CPUS      = 2
HOST_MEMORY    = 2048
HOST_BRIDGE    = "Hyper-V Virtual Ethernet Adapter #2"
HOST_USER      = "root"
HOST_USER_PASS = "Qq12345"
```
Измените их под себя.

## Запуск
В каталоге с Vagrantfile:
```powershell
vagrant validate          # Проверка синтаксиса
vagrant up                # Поднимает все ВМ
vagrant up server-vm01    # Поднимает одну ВМ
```

Подключение по SSH:
```powershell
vagrant ssh server-vm01
```

## Остановка и удаление
```powershell
vagrant halt              # Остановка всех ВМ
vagrant destroy -f        # Удаление всех ВМ
```
