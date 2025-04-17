# ПРАКТИЧЕСКАЯ РАБОТА No4 Работа c systemd

## 0. Заходим на виртуалочку:
```bash
ssh -l ivanovfedor 51.250.5.87
```

## 1.1 Выведите информацию о времени, затраченном на загрузку системы

Как сказано в методичке, мы можем получить информацию о времени запуске системы при помощи `systemd-analyze`
```bash
systemd-analyze
Startup finished in 3.522s (kernel) + 11.331s (userspace) = 14.853s 
graphical.target reached after 9.826s in userspace.
```

## 1.2 ыведите список всех запущенных при страте системы сервисов, в порядке уменьшения времени, затраченного на загрузку сервиса.

Тут есть пара уточнений:
* Верим, что я не запустил никаких сторонних сервисов
* Игнорируем `oneshot`, потому что достать время,  за которое они запустились, немного нетривиально

`systemd-analyze blame` выводит список заупщенных сейчас юнитов и время, за которое они запустились. Сортировка по умолчанию в порядке уменьшения.

```bash
systemd-analyze blame
4.442s cloud-init.service
2.174s cloud-init-local.service
1.227s cloud-config.service
 956ms ifupdown-pre.service
 934ms networking.service
 836ms systemd-binfmt.service
 757ms dev-vda2.device
 430ms cloud-final.service
 345ms systemd-udevd.service
 303ms apt-daily-upgrade.service
 165ms systemd-udev-trigger.service
 164ms apparmor.service
 130ms logrotate.service
 119ms modprobe@fuse.service
 117ms keyboard-setup.service
 116ms fstrim.service
 113ms systemd-machine-id-commit.service
 109ms user@1000.service
 100ms dbus.service
  99ms systemd-journal-flush.service
  95ms e2scrub_reap.service
  92ms systemd-journald.service
  90ms systemd-logind.service
  77ms systemd-random-seed.service
  67ms systemd-tmpfiles-setup-dev.service
  63ms ntpsec.service
  62ms systemd-tmpfiles-setup.service
  54ms modprobe@drm.service
  51ms modprobe@configfs.service
  49ms sys-kernel-tracing.mount
  49ms kmod-static-nodes.service
  49ms systemd-sysusers.service
  48ms dev-hugepages.mount
  48ms dev-mqueue.mount
  48ms sys-kernel-debug.mount
  47ms systemd-sysctl.service
  44ms systemd-modules-load.service
  44ms systemd-remount-fs.service
  20ms sys-kernel-config.mount
  19ms ssh.service
  19ms systemd-user-sessions.service
  13ms console-setup.service
   9ms systemd-tmpfiles-clean.service
   9ms systemd-update-utmp.service
   8ms modprobe@dm_mod.service
   7ms systemd-update-utmp-runlevel.service
   7ms proc-sys-fs-binfmt_misc.mount
   7ms modprobe@efi_pstore.service
   6ms modprobe@loop.service
   6ms user-runtime-dir@1000.service
   1ms sys-fs-fuse-connections.mount
   1ms e2scrub_all.service
```

## 1.3 Выведите список сервисов, запуск которых с необходимостью предшествовал запуску сервиса sshd

`systemd-analyze critical-chain` выдает нам список юнитов, котоыре ыбло необходимости запустить перед указанным. И все это в формате дерева.

Еще одно допущение: тут не отобразятся oneshot юниты, для которых не выставлен параметр RemainAfterExit

```bash
systemd-analyze critical-chain sshd.service
The time when unit became active or started is printed after the "@" c>
The time the unit took to start is printed after the "+" character.

ssh.service +19ms
└─basic.target @9.607s
  └─sockets.target @9.607s
    └─dbus.socket @9.606s
      └─sysinit.target @9.550s
        └─cloud-init.service @5.097s +4.442s
          └─networking.service @4.159s +934ms
            └─network-pre.target @4.119s
              └─cloud-init-local.service @1.943s +2.174s
                └─systemd-remount-fs.service @1.873s +44ms
                  └─systemd-journald.socket @1.809s
                    └─-.mount @1.750s
                      └─-.slice @1.750s
```

## 1.4 Сформируйте изображение в формате svg с графиком загрузки системы, сохраните его в файл.

`systemd-analyze plot` отдаст svg картинку, на кооторой порядок и время запуска юнитов.

```bash
systemd-analyze plot > bootplot.svg
```

```bash
scp ivanovfedor@51.250.5.87:/home/ivanovfedor/bootplot.svg .
```

![boot-plot](bootplot.svg)

## 2.1 Получите список всех запущенных юнитов сервисов

`systemctl list-units` отдаст списк юнитов конкретного типа и статуса.

```bash
systemctl list-units --type=service --state=running


  UNIT                        LOAD   ACTIVE SUB     DESCRIPTION                                   
  cron.service                loaded active running Regular background program processing daemon
  dbus.service                loaded active running D-Bus System Message Bus
  getty@tty1.service          loaded active running Getty on tty1
  ntpsec.service              loaded active running Network Time Service
  serial-getty@ttyS0.service  loaded active running Serial Getty on ttyS0
  ssh.service                 loaded active running OpenBSD Secure Shell server
  systemd-journald.service    loaded active running Journal Service
  systemd-logind.service      loaded active running User Login Management
  systemd-udevd.service       loaded active running Rule-based Manager for Device Events and Files
  unattended-upgrades.service loaded active running Unattended Upgrades Shutdown
  user@1000.service           loaded active running User Manager for UID 1000

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.
11 loaded units listed.
```

## 2.2 Выведите перечень всех юнитов сервисов, для которых назначена автозагрузка.

`systemctl list-unit-files` отдаст файлы с настройками юнитов

```bash
systemctl list-unit-files --state=enabled

UNIT FILE                   STATE   PRESET 
acpid.path                  enabled enabled
ntpsec-systemd-netif.path   enabled enabled
apparmor.service            enabled enabled
cloud-config.service        enabled enabled
cloud-final.service         enabled enabled
cloud-init-local.service    enabled enabled
cloud-init.service          enabled enabled
console-setup.service       enabled enabled
cron.service                enabled enabled
e2scrub_reap.service        enabled enabled
getty@.service              enabled enabled
keyboard-setup.service      enabled enabled
networking.service          enabled enabled
ntpsec.service              enabled enabled
ssh.service                 enabled enabled
systemd-pstore.service      enabled enabled
unattended-upgrades.service enabled enabled
acpid.socket                enabled enabled
cloud-init-hotplugd.socket  enabled enabled
remote-fs.target            enabled enabled
apt-daily-upgrade.timer     enabled enabled
apt-daily.timer             enabled enabled
dpkg-db-backup.timer        enabled enabled
e2scrub_all.timer           enabled enabled
fstrim.timer                enabled enabled
logrotate.timer             enabled enabled
man-db.timer                enabled enabled
ntpsec-rotate-stats.timer   enabled enabled

28 unit files listed.
```

## 2.3 Определите от каких юнитов зависит сервис sshd

Тут все просто. Смотрим через `systemctl` от кого прям зависит, а кого хотелось бы, но не обязательно

```bash
systemctl show sshd --property=Requires,Wants
Requires=-.mount system.slice sysinit.target
Wants=
```

## 2.4 Определите запущен ли сервис cron, если нет, запустите его.
```bash
systemctl status cron
● cron.service - Regular background program processing daemon
     Loaded: loaded (/lib/systemd/system/cron.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-04-16 21:46:23 UTC; 2h 34min ago
       Docs: man:cron(8)
   Main PID: 665 (cron)
      Tasks: 1 (limit: 4650)
     Memory: 444.0K
        CPU: 27ms
     CGroup: /system.slice/cron.service
             └─665 /usr/sbin/cron -f

Warning: some journal files were not opened due to insufficient permissions.
```

## 2.5 Выведите все параметры юнита cron, даже те, которые были назначены автоматически, и не были прописаны в файле юнита.

И там дальше еще много.

```bash
systemctl show cron
Type=simple
ExitType=main
Restart=on-failure
NotifyAccess=none
RestartUSec=100ms
TimeoutStartUSec=1min 30s
TimeoutStopUSec=1min 30s
TimeoutAbortUSec=1min 30s
TimeoutStartFailureMode=terminate
TimeoutStopFailureMode=terminate
RuntimeMaxUSec=infinity
RuntimeRandomizedExtraUSec=0
WatchdogUSec=0
WatchdogTimestampMonotonic=0
RootDirectoryStartOnly=no
RemainAfterExit=no
...
```

## 2.6 Запретите автозагрузку сервиса cron, но оставите ему возможность запускаться по зависимостям.

disabled сервисы сами по себе не запускаются при загрузке. Но запускаются, если являются звисимостями. 

```bash
sudo systemctl disable cron
Synchronizing state of cron.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install disable cron
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
Removed "/etc/systemd/system/multi-user.target.wants/cron.service".
```

## 3.1 Создайте собственный сервис mymsg

Конфиг сервиса положим сюда `/etc/systemd/system/mymsg.service`. Вот содержимое конфига:
```
[Unit]
Description=Ivanovfedor message service
Requires=netsork.target
After=network.target

[Service]
Type=oneshot
ExecStart=/bin/sh -c 'logger "mymsg: Service started. Current date: $(date)"'

[Install]
WantedBy=multi-user.target
```

## 3.2 Настройте автоматический запуск сервиса mymsg при старте системы.


```bash
sudo systemctl enable mymsg
Created symlink /etc/systemd/system/multi-user.target.wants/mymsg.service → /etc/systemd/system/mymsg.service.
```

## 3.3 Запустите сервис.
```bash
sudo systemctl start mymsg.service
```

## 4.1 Выведите на консоль системный журнал.

```bash
sudo journalctl
```

## 4.2 Выведите на консоль все сообщения системного журнала, касающиеся сервиса mymsg.

```bash
sudo journalctl -u mymsg
# Там еще много что т.к у меня опечатки были, но неважно
Apr 17 00:33:35 linux-labs-vm root[5693]: mymsg: Service started. Current date: Thu Apr 17 12:33:35 AM UTC 2025
```

## 4.3 Выведите на экран все сообщения об ошибках в журнале.

Тут можно отследить мои опечтаки.
```bash
sudo journalctl -p err
Apr 16 21:46:23 linux-labs-vm ntpd[684]: CONFIG: restrict nopeer ignored
Apr 16 21:46:23 linux-labs-vm ntpd[684]: statistics directory /var/log/ntpsec/ does not exist or is unwriteable, error No such file or directory
Apr 16 21:54:39 linux-labs-vm sshd[1159]: error: kex_exchange_identification: Connection closed by remote host
Apr 16 23:00:07 linux-labs-vm sshd[4513]: error: kex_exchange_identification: Connection closed by remote host
Apr 17 00:29:22 linux-labs-vm (bassh)[5578]: mymsg.service: Failed at step EXEC spawning /bin/bassh: No such file or directory
Apr 17 00:29:22 linux-labs-vm systemd[1]: Failed to start mymsg.service - Ivanovfedor messgae service.
Apr 17 00:29:35 linux-labs-vm (bassh)[5585]: mymsg.service: Failed at step EXEC spawning /bin/bassh: No such file or directory
Apr 17 00:29:35 linux-labs-vm systemd[1]: Failed to start mymsg.service - Ivanovfedor messgae service.
Apr 17 00:30:08 linux-labs-vm sshd[5591]: error: kex_exchange_identification: Connection closed by remote host
Apr 17 00:30:17 linux-labs-vm (bassh)[5599]: mymsg.service: Failed at step EXEC spawning /bin/bassh: No such file or directory
Apr 17 00:30:17 linux-labs-vm systemd[1]: Failed to start mymsg.service - Ivanovfedor messgae service.
Apr 17 00:32:08 linux-labs-vm (bassh)[5627]: mymsg.service: Failed at step EXEC spawning /bin/bassh: No such file or directory
Apr 17 00:32:08 linux-labs-vm systemd[1]: Failed to start mymsg.service - Ivanovfedor messgae service.
Apr 17 00:36:43 linux-labs-vm sshd[5746]: error: kex_exchange_identification: Connection closed by remote host
```

## 4.4 Определите размер журнала.
```bash
sudo journalctl --disk-usage
Archived and active journals take up 16.0M in the file system.
```

## 5.1 Подготовьте файловую систему.

```bash
lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
vda    254:0    0  100G  0 disk 
|-vda1 254:1    0    1M  0 part 
`-vda2 254:2    0  100G  0 part /
vdb    254:16   0   20G  0 disk 
```
```bash
sudo fdisk /dev/vdb

Welcome to fdisk (util-linux 2.38.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS (MBR) disklabel with disk identifier 0x2947539c.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-41943039, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943039, default 41943039): 

Created a new partition 1 of type 'Linux' and of size 20 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
```bash
sudo lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
vda    254:0    0  100G  0 disk 
|-vda1 254:1    0    1M  0 part 
`-vda2 254:2    0  100G  0 part /
vdb    254:16   0   20G  0 disk 
`-vdb1 254:17   0   20G  0 part 
```

```bash
sudo mkfs.ext4 /dev/vdb1
mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 5242624 4k blocks and 1310720 inodes
Filesystem UUID: f8c20e74-4114-4f6e-ad54-90d0d94dcb3a
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): 
done
Writing superblocks and filesystem accounting information: done   
```

```bash
sudo mkdir /mnt/vdb1
```

## 5.2 Создание .mount юнита

`etc/systemd/system/mnt-vdb1.mount`
```
[Unit]
Description=Mount /mnt/vdb1

[Mount]
What=/dev/vdb1
Where=/mnt/vdb1
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
```

## 5.3 Запуск и проверка .mount юнита

```bash
ivanovfedor@linux-labs-vm:~$ sudo systemctl start mnt-vdb1.mount^C
ivanovfedor@linux-labs-vm:~$ sudo systemctl status mnt-vdb1.mount
● mnt-vdb1.mount - Mount /mnt/vdb1
     Loaded: loaded (/etc/systemd/system/mnt-vdb1.mount; disabled; preset: enabled)
     Active: active (mounted) since Thu 2025-04-17 00:49:55 UTC; 25s ago
      Where: /mnt/vdb1
       What: /dev/vdb1
      Tasks: 0 (limit: 4650)
     Memory: 36.0K
        CPU: 3ms
     CGroup: /system.slice/mnt-vdb1.mount

Apr 17 00:49:55 linux-labs-vm systemd[1]: Mounting mnt-vdb1.mount - Mount /mnt/vdb1...
Apr 17 00:49:55 linux-labs-vm systemd[1]: Mounted mnt-vdb1.mount - Mount /mnt/vdb1.
```

```bash
mount -l | grep "vdb1"
/dev/vdb1 on /mnt/vdb1 type ext4 (rw,relatime)
```

## 6.2 Подготовьте соответствующий .mount-юнит
`/etc/systemd/system/mnt-vdb1.automount`
```
[Unit]
Description= Autoumount /dev/vdb1

[Automount]
Where=/mnt/vdb1
TimeoutIdleSec=30

[Install]
WantedBy=multi-user.target
```


## 6.3 Запуск и проверка .automount юнита

```bash
sudo systemctl start mnt-vdb1.automount
```

```bash
mount -l | grep "vdb1"
systemd-1 on /mnt/vdb1 type autofs (rw,relatime,fd=41,pgrp=1,timeout=30,minproto=5,maxproto=5,direct,pipe_ino=45589)
```

# ШПОРЫ
## 1. Чем отличаются команды systemctl restart и systemctl try-restart?

systemctl restart
Перезапускает службу вне зависимости от её текущего состояния.
Если служба активна (running), она будет остановлена и запущена заново.
Если служба не запущена (not running), команда сначала попытается её запустить, а затем выполнит перезапуск.

Перезапускает службу только если она уже активна (т.е. работает в момент выполнения команды).
Если служба не запущена, команда не будет выполнять никаких действий.
Полезно, когда нужно избежать случайного запуска остановленной службы.
Пример:

## 2. Как с помощью systemctl запустить Linux в однопользовательском режиме?

`sudo systemctl isolate rescue.target` - временно
Система остановит все ненужные сервисы и переключится в режим с минимальным набором служб.
Будет смонтирована корневая файловая система в режиме только для чтения (если не настроено иное).
Для полного доступа к системе потребуется ввести пароль root.

`systemd.unit=rescue.target` - Добавьте параметр ядра при загрузке:

Важные нюансы

rescue.target vs emergency.target
rescue.target:
Загружает базовые сервисы, монтирует файловые системы.
Доступна сетевая подсистема (если настроена).
emergency.target:
Ещё более минималистичный режим (только initramfs).
Файловые системы не монтируются автоматически — требуется ручное монтирование.
Пароль root
Если пароль root не задан, система может запросить пароль вашего пользователя (зависит от настроек pam и polkit).