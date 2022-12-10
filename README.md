# Как запустить Java сервер на Android смартфоне
## 1. Установить Termux  
Скачать можно [отсюда](https://f-droid.org/en/packages/com.termux/), версия в гугл плее неактуальная. Запускаем, для нас откроется командная строка.
## 2. Установить ssh для удаленного доступа с ПК (опционально)  
1. Устанавливаем пакет с ssh и nmap (для упрощения дальнейшей установки): `pkg install openssh nmap wget`.  
2. Запускаем ssh сервер: `sshd`  
3. Далее узнаем наш никнейм и адрес в сети c помощью команд `whoami` и `ifconfig wlan0`:
```console
$ whoami
u0_a147
$ ifconfig wlan0
wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.105  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::ce9f:7aff:fe81:3115  prefixlen 64  scopeid 0x20<link>
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
        txqueuelen 3000  (UNSPEC)
        RX packets 227165  bytes 311846650 (297.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 82264  bytes 7912862 (7.5 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
4. Далее нужно проверить на каком порте запущен ssh `nmap -sV 127.0.0.1`:
```console
$ nmap -sV 127.0.0.1
Starting Nmap 7.91 ( https://nmap.org ) at 2021-03-12 14:44 IST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0075s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE VERSION
8022/tcp open  ssh     OpenSSH 8.4 (protocol 2.0)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.87 seconds
```
5. Установим пароль для доступа к нашему серверу командой `passwd` (пароль не печатается в консоль):
```console
$ passwd
New password:
Retype new password:
New password was successfully set.
```
6. Далее подключаемся к нашему устройству с ПК по схеме:
```console
$ ssh -p <PORT> <USER>@<IP>
```
В нашем случае:
```console
$ ssh -p 8022 u0_a147@192.168.0.105
```
## 2. Обновляем базу пакетов и скачиваем необходимые 
Пишем в консоль: `apt update && apt upgrade`.

Устанавливаем wget: `pkg install wget`.
## 3. Устанавливаем Java 17  
Сначала вводим просто `java`, Termux сам напишет команду для установки Java, в нашем случае это `pkg install openjdk-17`.
## 4. Скачиваем наш fat.jar с сервером
1. Сначала нужно получить прямую ссылку на скачивание нашего сервера. Я просто закинул .jar в [этот](https://anonfiles.com) файлообменник, и скопировал прямую ссылку.
2. Дальше пишем в консоле: `wget <наша-прямая-ссылка>`.
## 5. Запускаем наш сервер
~~~console
$ java -jar <file-name>.jar
~~~
## 6. Настраиваем доступ из интернета использую ngrok
1. Скачиваем архив с программой с официального сайта:
~~~console
$ wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-arm64.tgz
~~~
2. Разархивируем файл:
~~~console
$ tar -xvzf ngrok-stable-linux-arm64.tgz
~~~
3. Регистрируемся на официальном сайте и получаем наш токен авторизации.
4. Далее добавляем наш токен в стандарный конфиг-файл:
~~~console
$ ./ngrok authtoken <token>
~~~
5. Включаем интернет для сим-карты на телефоне и раздачу wi-fi (без этого шага ngrok не может подключиться к серверу)
6. Включаем доступ к нашему серверу по адресу localhost:8080
~~~console
$ ./ngrok http 8080
~~~
<img src="/images/Port-Forwarding-With-Ngork-1024x543.png">
7. Теперь мы получили ссылку для доступа к нашему серверу из интернета!
<pre>
<a href="http://ded9762240de.ngrok.io">http://ded9762240de.ngrok.io</a>
</pre>
