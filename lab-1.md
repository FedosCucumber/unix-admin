# Лаборотораня работа номер 1
## Задание 0 - поднять вм
Вместо поднятися локальной виртуальной машины использовал облачную. Отсюда изменени я в некоторых файлах / именах

## задание 1
Инфомрацию о пользователях можно извлечь из файла `/etc/passwd`.
При помощи `nano` создаем скрипт `list_users.sh` (альтерантивы - awk, xargs и тд. Но не очень элегантно)
```bash
while IFS=: read  name _ id _; do
  echo "User $name has id $id"
done < /etc/passwd
```
Исполняем скрипт и выводим результат в файл `work3.log`
```bash
bash list_users.sh > work3.log
```
Смотрим результат:
```bash
cat work3.log
```
```
User root has id 0
User daemon has id 1
User bin has id 2
User sys has id 3
User sync has id 4
User games has id 5
User man has id 6
User lp has id 7
User mail has id 8
User news has id 9
User uucp has id 10
User proxy has id 13
User www-data has id 33
User backup has id 34
User list has id 38
User irc has id 39
User _apt has id 42
User nobody has id 65534
User systemd-network has id 998
User messagebus has id 100
User sshd has id 101
User ntpsec has id 102
User tcpdump has id 103
User admin-mua has id 1000
```

## Задание 2
При помощи команды `passwd` с флагом `-S` можно узнать, когда последний раз менял пароль определенного пользователя (и еще много всего). при помощи `cut` можем выбрать нужное поле из вывод команды. Результат команды выводим в конец файла `work3.log`.  Таким образом получаем
```bash
echo "root user last change password on "$(sudo passwd -S root | cut -d' ' -f3) >> work3.log
```
Смотрим, что у нас теперь в последней строчке файла:
```bash
tail -n1 work3.log
```
```
root user last change password on 2024-11-21
```

## Задание 3
Информацию о группах можно извлечь из файла `/etc/group`. Получаем команду:
```bash
echo $(cut -d ":" -f 1  /etc/group) | tr " " "," >> work3.log
```
Смотрим, что у нас теперь в последней строчке файла:
```bash
tail -n1 work3.log
```
```
root,daemon,bin,sys,adm,tty,disk,lp,mail,news,uucp,man,proxy,kmem,dialout,fax,voice,cdrom,floppy,tape,sudo,audio,dip,www-data,backup,operator,list,irc,src,shadow,utmp,video,sasl,plugdev,staff,games,users,nogroup,systemd-journal,systemd-network,crontab,input,sgx,kvm,render,netdev,messagebus,_ssh,ntpsec,tcpdump,admin-mua
```

## Задание 4

Создаем файл `/etc/skel/readme.txt`, который содерджит в себе сообщение `"Be careful!"`.
Альтерантивно можно создать `/usr/local/sbin/adduser.local`, который будет создавать файл.

Интерактивно создаем пользователя через `adduser`
```bash
sudo adduser test-readme
```
Смотрим, что лежит в его домашней директории:
```bash
sudo ls /home/test-readme/
```
```
readme.txt
```
Смотрим полученный файл:
```bash
sudo cat /home/test-readme/readme.txt
```
```
Be careful!
```

## Залание 5
```bash 
sudo useradd u1
echo "u1:12345678" | sudo chpasswd
```

```bash
sudo tail -n1 /etc/passwd
```
```
u1:x:1003:1003::/home/u1:/bin/sh
```

## Задание 6
```bash
sudo groupadd g1
```

## Задание 7
```bash
sudo usermod -a -G g1 u1
```
```bash
sudo tail -n1 /etc/
```
```
g1:x:1004:u1
```

## Задание 8
```bash
echo "User u1 with id $(id --u u1) is in groups: $(id -G u1)" >> work3.log 
```
```bash
tail -n1 work3.log
```
```
User u1 with id 1003 is in groups: 1003 1004
```

## Задание 9
```bash
sudo useradd user
sudo usermod -a -G g1 user
```

## Задание 10
```bash
echo "Group g1 has users: "$(grep "^g1:" /etc/group | cut -d":" -f 4)
```
```bash
tail -n1 work3.log 
```
```
Group g1 has users: u1,user
```

## Задание 11
Т.е образ у меня нестандартный, то мне нужно сначал установить получночного командира. Так же нужно создать домашнюю директорию для пользовталея u1 т.к его я добавлял через `useradd`.
```bash
sudo apt-get update
sudo apt-get install mc
sudo usermod -s /usr/bin/mc "u1"
sudo mkhomedir_helper u1
sudo chmod 777 /usr/bin/mc
su u1
```

##  Задание 12

```bash
sudo useradd u2
echo "u2:87654321" | sudo chpasswd
```

## Задание 13

```bash
sudo mkdir /home/test13
sudo cp "work3.log" /home/test13/work3-1.log
sudo cp "work3.log" /home/test13/work3-2.log
```

```bash
ls /home/test13
```
```
work3-1.log  work3-2.log
```

## Задание 14
```bash
sudo usermod -a -G "g1" "u2"
sudo chown u1:g1 -R /home/test13
sudo chmod 750 /home/test13
sudo chmod -R 640  /home/test13/.
```

## Задание 15
```bash
sudo mkdir /home/test14
sudo chmod 1777 /home/test14
sudo chown u1 /home/test14
```

## Задание 16
```bash
cp /usr/bin/nano /home/test14
chmod u+s /home/test14/nano
chmod u+s /home/test14/nano
```

## Задание 17
```bash
sudo mkdir /home/test15
sudo touch /home/test15/secret_file
sudo -i
echo "secret" >> /home/test15/secret_file
exit
sudo chmod a-r /home/test15
```

## Задание 18
```bash
sudo -i
set +H
rule="u1 ALL = /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd *root*"
echo "$rule" >> /etc/sudoers
exit
```
