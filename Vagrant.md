# Vagrant: команды, Vagrantfile и свой box

[Назад к главному README](README.md)

## Содержание
- [Жизненный цикл ВМ](#жизненный-цикл-вм)
- [SSH, снимки, плагины](#ssh-снимки-плагины)
- [Управление box’ами](#управление-boxами)
- [Полезные переменные окружения](#полезные-переменные-окружения)
- [Vagrantfile: расширенные опции](#vagrantfile-расширенные-опции)
  - [config.vm.*](#configvm)
  - [Сети](#сети)
- [Создание собственного box](#создание-собственного-box)

---

## Жизненный цикл ВМ
- `vagrant init <box>` — сгенерировать Vagrantfile.
- `vagrant up [--provider <prov>] [--provision]` — создать и запустить ВМ.
  - `vagrant up --provider=hyperv`.
- `vagrant halt [-f]` — корректно выключить.
- `vagrant suspend` / `vagrant resume` — спячка и возврат.
- `vagrant reload [--provision]` — перезапуск с пересборкой сетей.
- `vagrant provision` — только провижининг.
- `vagrant destroy [-f]` — удалить ВМ.
- `vagrant status` — статус текущего окружения.
- `vagrant global-status [--prune]` — все окружения.
- `vagrant validate` — проверка Vagrantfile.
- `vagrant version` — проверка версию Vagrant.
- `vagrant package --base name-vm-virtualbox` - создать box.

## SSH, снимки, плагины
- `vagrant ssh` — вход по SSH.
- `vagrant ssh-config` — блок для `~/.ssh/config`.
- `vagrant snapshot save|list|restore|delete|pop` — снимки, если поддерживает провайдер.
- `vagrant plugin list|install|uninstall|repair` — плагины.

## Управление box’ами
- `vagrant box add --name <user/box> <path|url> [--provider <prov>]` — добавить box.
  - `vagrant box add hashicorp/bionic64 --provider hyperv`.
- `vagrant box list` — список box’ов.
- `vagrant box remove <name> [--provider <prov>] [--box-version <ver>]` — удалить.
- `vagrant box outdated` — проверить обновления.
- `vagrant box update` — скачать новую версию для проекта.

## Полезные переменные окружения
- `VAGRANT_DEFAULT_PROVIDER=virtualbox|libvirt|vmware_desktop|hyperv|docker` — провайдер по умолчанию.
- `VAGRANT_CWD=/path/to/project` — работать из другого каталога.
- `VAGRANT_HOME=/path` — кэш box’ов и плагинов.
- `VAGRANT_LOG=debug` — подробный лог.
- `VAGRANT_NO_COLOR=1` — монохромный вывод.
- `VAGRANT_DISABLE_STRICT_DEPENDENCY_ENFORCEMENT=1` — смягчить проверки зависимостей плагинов.

---


## Vagrantfile: расширенные опции

### `config.vm.*`
| Опция | Тип | Назначение |
|---|---|---|
| `config.vm.box` | String | Имя базового box’а `user/name` или локального box’a. |
| `config.vm.box_version` | String | Требуемая версия box’а (`1.2.3`, `>= 1.2`). |
| `config.vm.box_url` | String/Array | URL `.box` или `metadata.json` либо список зеркал. |
| `config.vm.box_download_checksum` | String | Контрольная сумма box (`sha256:...`). |
| `config.vm.box_check_update` | Bool | Проверять обновления при `vagrant up` (по умолчанию `true`). |
| `config.vm.hostname` | String | Имя хоста в гостевой ОС. |
| `config.vm.boot_timeout` | Integer | Таймаут ожидания загрузки, сек. |
| `config.vm.graceful_halt_timeout` | Integer | Таймаут мягкого выключения, сек. |
| `config.vm.allowed_synced_folder_types` | Array | Разрешенные типы синхронизации. |
| `config.vm.post_up_message` | String | Сообщение после `vagrant up`. |
| `config.vm.define(name, primary: true)` | Block | Мульти‑ВМ. `primary` — ВМ по умолчанию. |
| `config.vm.provider "<name>"` | Block | Настройки провайдера. |
| `config.vm.provision "<type>"` | Block | Провижининг. |
| `config.vm.communicator` | String | `ssh` или `winrm`. |
| `config.vm.guest` | String | Тип гостя, редко нужно. |
| `config.vm.usable_port_range` | Range | Диапазон для автоподбора портов. |
| `config.vm.hostname = nil` | — | Не менять hostname. |

### Сети
Типы: `forwarded_port`, `private_network`, `public_network`.

#### Переадресация портов
```ruby
config.vm.network "forwarded_port",
  guest: 80, host: 8080,
  protocol: "tcp", auto_correct: true,
  host_ip: "127.0.0.1", guest_ip: "0.0.0.0"
```
| Ключ | Тип | Значение |
|---|---|---|
| `guest` | Integer | Порт в госте. |
| `host` | Integer | Порт на хосте. |
| `auto_correct` | Bool | Автосдвиг при конфликте. |
| `protocol` | String | `tcp` или `udp`. |
| `host_ip` | String | Привязка к IP хоста. |
| `guest_ip` | String | Привязка к IP гостя. |
| `id` | String | Явный идентификатор правила. |

#### Приватная сеть
```ruby
config.vm.network "private_network", ip: "192.168.56.10", netmask: "255.255.255.0", iface: nil, dhcp: false
```
| Ключ | Тип | Значение |
|---|---|---|
| `ip` | String | Статический IP. |
| `netmask` | String | Маска. |
| `dhcp` | Bool | DHCP вместо статики. |
| `iface` | String | Явной интерфейс. |

#### Публичная сеть (мост)
```ruby
config.vm.network "public_network", bridge: nil, ip: nil, use_dhcp_assigned_default_route: false
```
| Ключ | Тип | Значение |
|---|---|---|
| `bridge` | String/Regexp | Какой хостовой интерфейс мостировать. |
| `ip` | String | Статический IP. |
| `use_dhcp_assigned_default_route` | Bool | Делать DHCP‑маршрут дефолтным. |

---

## Создание собственного box

### `vagrant package`
1) Поднимите ВМ и доведите её до нужного состояния в VirtualBox.  
2) Упакуйте:
   - Из текущего окружения: `vagrant package --output mybox-1.0.0-virtualbox.box`
   - Из VM VirtualBox: `vagrant package --base "VM Name" --output mybox-1.0.0-virtualbox.box`
   - Добавьте дефолтный Vagrantfile: `--vagrantfile Vagrantfile`
3) Добавьте локально:  
   `vagrant box add --name myorg/mybox mybox-1.0.0-virtualbox.box --provider virtualbox`

### Внутри гостя (AlmaLinux)
```bash
# пользователь и sudo без пароля
useradd -m -s /bin/bash vagrant
echo 'vagrant:vagrant' | chpasswd
echo 'vagrant ALL=(ALL) NOPASSWD: ALL' >/etc/sudoers.d/vagrant
chmod 440 /etc/sudoers.d/vagrant

# ssh и insecure key
dnf -y install openssh-server curl
systemctl enable --now sshd
install -d -m 700 -o vagrant -g vagrant /home/vagrant/.ssh
curl -L https://raw.githubusercontent.com/hashicorp/vagrant/master/keys/vagrant.pub \
  -o /home/vagrant/.ssh/authorized_keys
chown vagrant:vagrant /home/vagrant/.ssh/authorized_keys
chmod 600 /home/vagrant/.ssh/authorized_keys

# VirtualBox Guest Additions
dnf -y install gcc make perl kernel-devel kernel-headers bzip2 elfutils-libelf-devel tar
curl -LO https://download.virtualbox.org/virtualbox/7.0.14/VBoxGuestAdditions_7.0.14.iso
mount -o loop VBoxGuestAdditions_7.0.14.iso /mnt
sh /mnt/VBoxLinuxAdditions.run
umount /mnt

# минимальная очистка и выключение
dnf -y clean all
shutdown now
```

### На хосте
```powershell
# упаковать VM по имени в VirtualBox
vagrant package --base "VM Name" --output almalinux9.6.box

# добавить и проверить
vagrant box add --name my/almalinux9.6 C:\DiskD\Vagrant\boxs\almalinux9.6.box --provider virtualbox
vagrant box list
```

---
