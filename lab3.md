# Лабораторная работа No 3 Установка программного обеспечения

## 0. Подключаемся на VM
```
ssh -l ivanovfedor 51.250.5.87
```

## 1. Выводим список репозиториев:
```bash
ivanovfedor@linux-labs-vm:~$ cat /etc/apt/sources.list
## Note, this file is written by cloud-init on first boot of an instance
## modifications made here will not survive a re-bundle.
## if you wish to make changes you can:
## a.) add 'apt_preserve_sources_list: true' to /etc/cloud/cloud.cfg
##     or do the same in user-data
## b.) add sources in /etc/apt/sources.list.d
## c.) make changes to template file /etc/cloud/templates/sources.list.debian.tmpl
###

# See http://www.debian.org/releases/stable/i386/release-notes/ch-upgrading.html
# for how to upgrade to newer versions of the distribution.
deb http://mirror.yandex.ru/debian/ bookworm main
deb-src http://mirror.yandex.ru/debian/ bookworm main

## Major bug fix updates produced after the final release of the
## distribution.
deb http://security.debian.org/ bookworm-security main
deb-src http://security.debian.org/ bookworm-security main
deb http://mirror.yandex.ru/debian/ bookworm-updates main
deb-src http://mirror.yandex.ru/debian/ bookworm-updates main

## Uncomment the following two lines to add software from the 'backports'
## repository.
## 
## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
deb http://mirror.yandex.ru/debian/ bookworm-backports main
deb-src http://mirror.yandex.ru/debian/ bookworm-backports main
```
У меня достаточно необычный список из коробки. Виртуалка поднята в яндекс облак, поэтому тут дополнительно есть яндексовые зеркала для деб репозиториев. Очень пригодится на случай чебурнета и санкций. 

## 2. Обновите локальные индексы пакетов в менеджере пакетов.

```bash
sudo apt update
Get:1 http://mirror.yandex.ru/debian bookworm InRelease [151 kB]
Get:2 http://mirror.yandex.ru/debian bookworm-updates InRelease [55.4 kB]
Get:3 http://mirror.yandex.ru/debian bookworm-backports InRelease [59.4 kB]
Get:4 http://security.debian.org bookworm-security InRelease [48.0 kB]
Get:5 http://mirror.yandex.ru/debian bookworm/main Sources [9495 kB]
Get:6 http://mirror.yandex.ru/debian bookworm/main amd64 Packages [8792 kB]
Get:7 http://mirror.yandex.ru/debian bookworm/main Translation-en [6109 kB]
Get:8 http://mirror.yandex.ru/debian bookworm/main amd64 Contents (deb) [11.7 MB]
Get:9 http://security.debian.org bookworm-security/main Sources [152 kB]
Get:10 http://security.debian.org bookworm-security/main amd64 Packages [254 kB]
Get:11 http://security.debian.org bookworm-security/main Translation-en [150 kB]
Get:12 http://mirror.yandex.ru/debian bookworm/main all Contents (deb) [33.9 MB]
Get:13 http://mirror.yandex.ru/debian bookworm-updates/main Sources [796 B]
Get:14 http://mirror.yandex.ru/debian bookworm-updates/main amd64 Packages [512 B]
Get:15 http://mirror.yandex.ru/debian bookworm-updates/main Translation-en [360 B]
Get:16 http://mirror.yandex.ru/debian bookworm-updates/main all Contents (deb) [9095 B]
Get:17 http://mirror.yandex.ru/debian bookworm-backports/main Sources [269 kB]
Get:18 http://mirror.yandex.ru/debian bookworm-backports/main amd64 Packages [273 kB]
Get:19 http://mirror.yandex.ru/debian bookworm-backports/main Translation-en [232 kB]
Get:20 http://mirror.yandex.ru/debian bookworm-backports/main amd64 Contents (deb) [777 kB]
Get:21 http://mirror.yandex.ru/debian bookworm-backports/main all Contents (deb) [4193 kB]
Fetched 76.6 MB in 7s (11.1 MB/s)                                     
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
5 packages can be upgraded. Run 'apt list --upgradable' to see them.
N: Repository 'Debian bookworm' changed its 'firmware component' value from 'non-free' to 'non-free-firmware'
N: More information about this can be found online in the Release notes at: https://www.debian.org/releases/bookworm/amd64/release-notes/ch-information.html#non-free-split
```

## 3. Выведите информацию о метапакете build-essential
Команда `apt show` вывоидт информацию о запрашиваемом пакете
```bash
apt show build-essential
Package: build-essential
Version: 12.9
Priority: optional
Build-Essential: yes
Section: devel
Maintainer: Matthias Klose <doko@debian.org>
Installed-Size: 20.5 kB
Depends: libc6-dev | libc-dev, gcc (>= 4:10.2), g++ (>= 4:10.2), make, dpkg-dev (>= 1.17.11)
Tag: devel::packaging, interface::commandline, role::data, role::program,
 scope::utility, suite::debian
Download-Size: 7704 B
APT-Sources: http://mirror.yandex.ru/debian bookworm/main amd64 Packages
Description: Informational list of build-essential packages
 If you do not plan to build Debian packages, you don't need this
 package.  Starting with dpkg (>= 1.14.18) this package is required
 for building Debian packages.
 .
 This package contains an informational list of packages which are
 considered essential for building Debian packages.  This package also
 depends on the packages on that list, to make it easy to have the
 build-essential packages installed.
 .
 If you have this package installed, you only need to install whatever
 a package specifies as its build-time dependencies to build the
 package.  Conversely, if you are determining what your package needs
 to build-depend on, you can always leave out the packages this
 package depends on.
 .
 This package is NOT the definition of what packages are
 build-essential; the real definition is in the Debian Policy Manual.
 This package contains merely an informational list, which is all
 most people need.   However, if this package and the manual disagree,
 the manual is correct.
```

## 4. Установите метапакет build-essential с прищуром
```bash
sudo apt install build-essential
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  binutils binutils-common binutils-x86-64-linux-gnu cpp cpp-12
  dirmngr dpkg-dev fakeroot fontconfig-config fonts-dejavu-core g++
  g++-12 gcc gcc-12 gnupg gnupg-l10n gnupg-utils gpg gpg-agent
  gpg-wks-client gpg-wks-server gpgconf gpgsm libabsl20220623
  libalgorithm-diff-perl libalgorithm-diff-xs-perl
  libalgorithm-merge-perl libaom3 libasan8 libassuan0 libatomic1
  libavif15 libbinutils libc-dev-bin libc-devtools libc6-dev libcc1-0
  libcrypt-dev libctf-nobfd0 libctf0 libdav1d6 libde265-0 libdeflate0
  libdpkg-perl libfakeroot libfile-fcntllock-perl libfontconfig1
  libgav1-1 libgcc-12-dev libgd3 libgomp1 libgprofng0 libheif1
  libisl23 libitm1 libjbig0 libjpeg62-turbo libksba8 liblerc4
  liblsan0 libmpc3 libmpfr6 libnpth0 libnsl-dev libquadmath0
  librav1e0 libstdc++-12-dev libsvtav1enc1 libtiff6 libtirpc-dev
  libtsan2 libubsan1 libwebp7 libx265-199 libxpm4 libyuv0
  linux-libc-dev make manpages manpages-dev patch pinentry-curses
  rpcsvc-proto
Suggested packages:
  binutils-doc cpp-doc gcc-12-locales cpp-12-doc pinentry-gnome3 tor
  debian-keyring g++-multilib g++-12-multilib gcc-12-doc gcc-multilib
  autoconf automake libtool flex bison gdb gcc-doc gcc-12-multilib
  parcimonie xloadimage scdaemon glibc-doc git bzr libgd-tools
  libstdc++-12-doc make-doc man-browser ed diffutils-doc pinentry-doc
The following NEW packages will be installed:
  binutils binutils-common binutils-x86-64-linux-gnu build-essential
  cpp cpp-12 dirmngr dpkg-dev fakeroot fontconfig-config
  fonts-dejavu-core g++ g++-12 gcc gcc-12 gnupg gnupg-l10n
  gnupg-utils gpg gpg-agent gpg-wks-client gpg-wks-server gpgconf
  gpgsm libabsl20220623 libalgorithm-diff-perl
  libalgorithm-diff-xs-perl libalgorithm-merge-perl libaom3 libasan8
  libassuan0 libatomic1 libavif15 libbinutils libc-dev-bin
  libc-devtools libc6-dev libcc1-0 libcrypt-dev libctf-nobfd0 libctf0
  libdav1d6 libde265-0 libdeflate0 libdpkg-perl libfakeroot
  libfile-fcntllock-perl libfontconfig1 libgav1-1 libgcc-12-dev
  libgd3 libgomp1 libgprofng0 libheif1 libisl23 libitm1 libjbig0
  libjpeg62-turbo libksba8 liblerc4 liblsan0 libmpc3 libmpfr6
  libnpth0 libnsl-dev libquadmath0 librav1e0 libstdc++-12-dev
  libsvtav1enc1 libtiff6 libtirpc-dev libtsan2 libubsan1 libwebp7
  libx265-199 libxpm4 libyuv0 linux-libc-dev make manpages
  manpages-dev patch pinentry-curses rpcsvc-proto
0 upgraded, 84 newly installed, 0 to remove and 5 not upgraded.
Need to get 87.9 MB of archives.
After this operation, 332 MB of additional disk space will be used.
Do you want to continue? [Y/n] 
```

В моем случае будет установлено 84 пакета, 0 будет обновлено (5 не будет обновленов. всего 89 зависимостей, как я понял).

## 5. Найдите пакет, в описании которого присутствует строка «clone with a bastard algorithm»
Команда `apt search` выполнит поиск пакетов по ключевым словам (и прямым цитатам) в названиях и поисаниях пакетов.
```bash
apt search "clone with a bastard algorithm"
Sorting... Done
Full Text Search... Done
bastet/stable 0.43-7+b1 amd64
  ncurses Tetris clone with a bastard algorithm
```

## 6. Скачайте в отдельную директорию в домашнем каталоге архив с исходными кодами найденного в п.5 пакета.

Команда `apt-get source` скачивате архивы с исходниками приложения.

```bash
mkdir bastet-doghouse
cd bastet-doghouse/
```
```bash
apt-get source bastet
Reading package lists... Done
NOTICE: 'bastet' packaging is maintained in the 'Git' version control system at:
https://salsa.debian.org/games-team/bastet.git
Please use:
git clone https://salsa.debian.org/games-team/bastet.git
to retrieve the latest (possibly unreleased) updates to the package.
Need to get 43.5 kB of source archives.
Get:1 http://mirror.yandex.ru/debian bookworm/main bastet 0.43-7 (dsc) [2122 B]
Get:2 http://mirror.yandex.ru/debian bookworm/main bastet 0.43-7 (tar) [29.1 kB]
Get:3 http://mirror.yandex.ru/debian bookworm/main bastet 0.43-7 (diff) [12.3 kB]
Fetched 43.5 kB in 0s (1038 kB/s)
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
dpkg-source: info: extracting bastet in bastet-0.43
dpkg-source: info: unpacking bastet_0.43.orig.tar.gz
dpkg-source: info: unpacking bastet_0.43-7.debian.tar.xz
dpkg-source: info: using patch list from debian/patches/series
dpkg-source: info: applying 01_local_hiscores_file_check.patch
dpkg-source: info: applying 02_manpage_global_hiscore_path.patch
dpkg-source: info: applying debian-bug-931550.patch
dpkg-source: info: applying format-security.patch
```
```bash 
ls -a
.   bastet-0.43                  bastet_0.43-7.dsc
..  bastet_0.43-7.debian.tar.xz  bastet_0.43.orig.tar.gz
```

## 7. Установите пакет из исходных кодов, скаченных в п.6

Сначала скачаем все зависимости для сборки команда `apt-get build-dep`:
```bash
sudo apt-get build-dep bastet
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  autoconf automake autopoint autotools-dev bsdextrautils debhelper
  dh-autoreconf dh-strip-nondeterminism dwz gettext groff-base
  intltool-debian libarchive-zip-perl libboost-dev
  libboost-program-options-dev libboost-program-options1.74-dev
  libboost-program-options1.74.0 libboost1.74-dev libdebhelper-perl
  libfile-stripnondeterminism-perl libncurses-dev libncurses5-dev
  libpipeline1 libsub-override-perl libtool libuchardet0 m4 man-db
  po-debconf
0 upgraded, 29 newly installed, 0 to remove and 5 not upgraded.
Need to get 18.4 MB of archives.
After this operation, 173 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
```

Перейдем в каталог с иходниками и `bastet`:
```bash
cd bastet-0.43/
make
g++ -DNDEBUG -Wall   -c -o Ui.o Ui.cpp
Ui.cpp: In member function 'int Bastet::BorderedWindow::GetMinX()':
Ui.cpp:82:9: warning: variable 'y' set but not used [-Wunused-but-set-variable]
   82 |     int y,x;
      |         ^
Ui.cpp: In member function 'int Bastet::BorderedWindow::GetMaxX()':
Ui.cpp:94:9: warning: variable 'y' set but not used [-Wunused-but-set-variable]
   94 |     int y,x;
      |         ^
Ui.cpp: In function 'Bastet::Dot Bastet::BoundingRect(const std::string&)':
Ui.cpp:174:24: warning: narrowing conversion of '(maxlen + 1)' from 'size_t' {aka 'long unsigned int'} to 'int' [-Wnarrowing]
  174 |     return (Dot){maxlen+1,splits.size()};
      |                  ~~~~~~^~
Ui.cpp:174:38: warning: narrowing conversion of 'splits.std::vector<std::__cxx11::basic_string<char> >::size()' from 'std::vector<std::__cxx11::basic_string<char> >::size_type' {aka 'long unsigned int'} to 'int' -Wnarrowing]
  174 |     return (Dot){maxlen+1,splits.size()};
      |                           ~~~~~~~~~~~^~
Ui.cpp: In member function 'int Bastet::Ui::MenuDialog(const std::vector<std::__cxx11::basic_string<char> >&)':
Ui.cpp:236:17: warning: narrowing conversion of '(width + 5)' from 'size_t' {aka 'long unsigned int'} to 'int' [-Wnarrowing]
  236 |     Dot d={width+5,choices.size()};
      |            ~~~~~^~
Ui.cpp:236:32: warning: narrowing conversion of '(& choices)->std::vector<std::__cxx11::basic_string<char> >::size()' from 'std::vector<std::__cxx11::basic_string<char> >::size_type' {aka 'long unsigned int'} to int' [-Wnarrowing]
  236 |     Dot d={width+5,choices.size()};
      |                    ~~~~~~~~~~~~^~
g++ -DNDEBUG -Wall   -c -o main.o main.cpp
g++ -DNDEBUG -Wall   -c -o Block.o Block.cpp
g++ -DNDEBUG -Wall   -c -o Well.o Well.cpp
g++ -DNDEBUG -Wall   -c -o BlockPosition.o BlockPosition.cpp
g++ -DNDEBUG -Wall   -c -o Config.o Config.cpp
g++ -DNDEBUG -Wall   -c -o BlockChooser.o BlockChooser.cpp
g++ -DNDEBUG -Wall   -c -o BastetBlockChooser.o BastetBlockChooser.cpp
BastetBlockChooser.cpp: In member function 'virtual Bastet::BlockType Bastet::BastetBlockChooser::GetNext(const Bastet::Well*, const Bastet::Queue&)':
BastetBlockChooser.cpp:118:76: warning: 'std::binder2nd<_Operation> std::bind2nd(const _Operation&, const _Tp&) [with _Operation = greater_equal<int>; _Tp = long int]' is deprecated: use 'std::bind' instead [-Wdeprecated-declarations]
  118 | kPercentages.begin(),blockPercentages.end(),bind2nd(greater_equal<int>(),random()%100)) - blockPercentages.begin();
      |                                             ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In file included from /usr/include/c++/12/bits/stl_function.h:1438,
                 from /usr/include/c++/12/string:48,
                 from /usr/include/c++/12/bits/locale_classes.h:40,
                 from /usr/include/c++/12/bits/ios_base.h:41,
                 from /usr/include/c++/12/streambuf:41,
                 from /usr/include/c++/12/bits/streambuf_iterator.h:35,
                 from /usr/include/c++/12/iterator:66,
                 from /usr/include/boost/array.hpp:44,
                 from Block.hpp:22,
                 from BlockChooser.hpp:22,
                 from BastetBlockChooser.hpp:22,
                 from BastetBlockChooser.cpp:19:
/usr/include/c++/12/backward/binders.h:172:5: note: declared here
  172 |     bind2nd(const _Operation& __fn, const _Tp& __x)
      |     ^~~~~~~
BastetBlockChooser.cpp: In member function 'virtual Bastet::BlockType Bastet::NoPreviewBlockChooser::GetNext(const Bastet::Well*, const Bastet::Queue&)':
BastetBlockChooser.cpp:214:76: warning: 'std::binder2nd<_Operation> std::bind2nd(const _Operation&, const _Tp&) [with _Operation = greater_equal<int>; _Tp = long int]' is deprecated: use 'std::bind' instead [-Wdeprecated-declarations]
  214 | kPercentages.begin(),blockPercentages.end(),bind2nd(greater_equal<int>(),random()%100)) - blockPercentages.begin();
      |                                             ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

/usr/include/c++/12/backward/binders.h:172:5: note: declared here
  172 |     bind2nd(const _Operation& __fn, const _Tp& __x)
      |     ^~~~~~~
g++ -ggdb -o bastet Ui.o main.o Block.o Well.o BlockPosition.o Config.o BlockChooser.o BastetBlockChooser.o -lncurses -lboost_program_options
```

## 8. Шаманим настройку установки
Очень важно использоват таб!!!
```bash
echo 'install: bastet
        install -m755 bastet /usr/local/bin/bastet' >> Makefile
````
```bash
ls -l /usr/local/bin/bastet
-rwxr-xr-x 1 root root 693120 Apr 16 22:14 /usr/local/bin/bastet
```

## 9. Проверим, что все получилось
```bash
sudo adduser tester
sudo su tester

tester@linux-labs-vm:/home/ivanovfedor/bastet-doghouse/bastet-0.43$ /usr/local/bin/bastet
bastet: using a user-specific high scores file: /home/tester/.bastetscores
as the global high scores file /var/games/bastet.scores2 is not writable
bastet: creating a new user-specific high scores file /home/tester/.bastetscores
```

Фига, все это время я возился с консольным тетрисом.

## 10. Создайте файл task10.log, в который выведите список всех установленных пакетов
`dpkg -l` или `dpkg --list` отдаст нам списк всех установленных пакетов. 
```bash
dpkg -l > task10.log
```

## 11. Создайте файл task11.log, в который выведите список всех пакетов (зависимостей), необходимых для установки и работы компилятора gcc.

`apt-cache depends` в сыром виде покажет инфомрацию о зависимостях пакета.
```bash
apt-cache depends gcc > task11.log
```

## 12. Создайте файл task12.log, в который выведите список всех пакетов (зависимостей), установка которых требует установленного пакета libgpm2.

`apt-cache rdepends` показывает инвертированную (reverse) информацию про зависимости о пакете (таков мой вольный перевод фразы `rdepends - Show reverse dependency information for a package`)

```bash
apt-cache rdepends libgpm2 > task12.log
```
## 13. Наполняем localrepo

```bash
sudo su root
```
```bash
root@linux-labs-vm:~# cd ~
root@linux-labs-vm:~# pwd
/root
```
```bash
mkdir localrepo
```

посмотрим в [репозитории](http://snapshot.debian.org/archive/debian/20230227T025930Z/pool/main/h/htop/) какие есть версии и скачает несколько.

```bash
wget http://snapshot.debian.org/archive/debian/20230227T025930Z/pool/main/h/htop/htop_3.2.2-2_amd64.deb
wget http://snapshot.debian.org/archive/debian/20230227T025930Z/pool/main/h/htop/htop_3.2.2-1_amd64.deb
wget http://snapshot.debian.org/archive/debian/20230227T025930Z/pool/main/h/htop/htop_3.0.5-7_amd64.deb
wget http://snapshot.debian.org/archive/debian/20230227T025930Z/pool/main/h/htop/htop_2.2.0-1+b1_amd64.deb
wget http://snapshot.debian.org/archive/debian/20230227T025930Z/pool/main/h/htop/htop_2.0.2-1_amd64.deb
wget http://snapshot.debian.org/archive/debian/20230227T025930Z/pool/main/h/htop/htop_1.0.3-1_amd64.deb
```

```bash
root@linux-labs-vm:~/localrepo# ls -a
.			htop_2.2.0-1+b1_amd64.deb
..			htop_3.0.5-7_amd64.deb
htop_1.0.3-1_amd64.deb	htop_3.2.2-1_amd64.deb
htop_2.0.2-1_amd64.deb	htop_3.2.2-2_amd64.deb
```

## 14. Генерим метаданные о репозитории

Чтобы сгененировать манифест с доступными пакетами воспользуемы утилитой `dpkg-scanpackages`

```bash
dpkg-scanpackages --multiversion . > Packages
```
```bash
cat Packages 
Package: htop
Version: 1.0.3-1
Architecture: amd64
Maintainer: Eugene V. Lyubimkin <jackyf@debian.org>
Installed-Size: 204
Depends: libc6 (>= 2.15), libncursesw5 (>= 5.6+20070908), libtinfo5
Suggests: strace, ltrace
Filename: ./htop_1.0.3-1_amd64.deb
Size: 75316
MD5sum: 4e060d3f99a75447695028668ac43742
SHA1: 27b2382c359983eff8cc523b52ae692dff795040
SHA256: 1fe5cf9ea7ea289e4ecced2997b62113b6b2db681d1934d63b4849ad3bfbecad
Section: utils
Priority: optional
Homepage: http://hisham.hm/htop/
Description: interactive processes viewer
 Htop is an ncursed-based process viewer similar to top, but it
 allows one to scroll the list vertically and horizontally to see
 all processes and their full command lines.
 .
 Tasks related to processes (killing, renicing) can be done without
 entering their PIDs.
...
```

Дальше добавим описание репощитория:
```bash
echo -n 'Origin: My Local Repo
Label: My Local Repo
Suite: stable
Version: 1.0
Codename: myrepo
Architectures: amd64
Components: main
Description: My local APT repository' >> Release
```

## 15. Интермидия и добавление созданного мной репозитория в лкоальные.

Видимо, ожидается, что мой круто репозиторйи прорастет в доступные для apt.

```
cp -r localrepo/ /usr/local/repos/localrepo

echo "deb [trusted=yes] file:/usr/local/repos/localrepo ./" >> /etc/apt/sources.list

apt update
```

## 16. Выведите список подключенных репозитариях и краткую информацию о них.

```bash
apt-cache policy
Package files:
 100 /var/lib/dpkg/status
     release a=now
 500 file:/usr/local/repos/localrepo ./ Packages
     release v=1.0,o=My Local Repo,a=stable,n=myrepo,l=My Local Repo,c=
 100 http://mirror.yandex.ru/debian bookworm-backports/main amd64 Packages
     release o=Debian Backports,a=stable-backports,n=bookworm-backports,l=Debian Backports,c=main,b=amd64
     origin mirror.yandex.ru
 500 http://mirror.yandex.ru/debian bookworm-updates/main amd64 Packages
     release v=12-updates,o=Debian,a=stable-updates,n=bookworm-updates,l=Debian,c=main,b=amd64
     origin mirror.yandex.ru
 500 http://security.debian.org bookworm-security/main amd64 Packages
     release v=12,o=Debian,a=stable-security,n=bookworm-security,l=Debian-Security,c=main,b=amd64
     origin security.debian.org
 500 http://mirror.yandex.ru/debian bookworm/main amd64 Packages
     release v=12.10,o=Debian,a=stable,n=bookworm,l=Debian,c=main,b=amd64
     origin mirror.yandex.ru
Pinned packages:
```

## 17. Создайте файл task16.log в который выведите список всех доступных версий htop

```bash
apt list -a htop > task16.log

cat task16.log 
Listing...
htop/stable,stable,now 3.2.2-2 amd64 [installed]
htop/stable 3.2.2-1 amd64
htop/stable 3.0.5-7 amd64
htop/stable 2.2.0-1+b1 amd64
htop/stable 2.0.2-1 amd64
htop/stable 1.0.3-1 amd64
```
`
## 18. Установите предпоследнюю версию пакета
```bash
sudo apt install htop=3.2.2-1
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  lm-sensors lsof strace
The following packages will be DOWNGRADED:
  htop
0 upgraded, 0 newly installed, 1 downgraded, 0 to remove and 5 not upgraded.
Need to get 0 B/153 kB of archives.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 file:/usr/local/repos/localrepo ./ htop 3.2.2-1 [153 kB]
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
dpkg: warning: downgrading htop from 3.2.2-2 to 3.2.2-1
(Reading database ... 54299 files and directories currently installed.)
Preparing to unpack ..././htop_3.2.2-1_amd64.deb ...
Unpacking htop (3.2.2-1) over (3.2.2-2) ...
Setting up htop (3.2.2-1) ...
Processing triggers for man-db (2.11.2-2) ...
```

## 19. Скачайте из сетевого репозитория пакет nano и пошаманьте

`dpkg-deb` - мультитул для работы с deb-пакетами и их сборкой.
Чтобы после установки пакета у нас случались дополнительыне действия необходимо создать файл `DEBIAN/postinst` с командами, которые должны выполняться.

```bash
apt download nano 
mkdir -p /tmp/nano-pkg
dpkg-deb -R nano* /tmp/nano-pkg
dpkg-deb -b /tmp/nano-pkg/ newnano.deb
dpkg-deb: building package 'nano' in 'newnano.deb'.
sudo dpkg -i newnano.deb
(Reading database ... 54299 files and directories currently installed.)
Preparing to unpack newnano.deb ...
Unpacking nano (7.2-1+deb12u1) overu (7.2-1+deb12u1) ...
Setting up nano (7.2-1+deb12u1) ...
Processing triggers for man-db (2.11.2-2) ...
```

```bash
ivanovfedor@linux-labs-vm:~$ mkdir some-dir
ivanovfedor@linux-labs-vm:~$ cd some-dir/
ivanovfedor@linux-labs-vm:~/some-dir$ newnano
```

## 20. Бонусный вопрос с подвохом - что есть в APT?

Если это английские буквы, то аббревиатура значит Advanced Packaging Tool. это утилита (а лучше сказать семейство утилит) для работы с деб-пакетами. 

А еще это могут быть русские буквы. Экстраполируем до слова "art". А в искусстве вся соль жизни...