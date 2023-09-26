# Установка WireGuard VPN

Установка производится на удаленном VPS уровня 1/1/10. Будем использовать установочный скрипт, который сам произведет добавление зависимостей, настройку служб и добавление пользователей.

В процессе установки нам понадобится внешний IP адрес сервера и название сетевого интерфейса

- Получаем и записываем необходимые параметры

```bash 
IP=$(curl checkip.amazonaws.com)
IF=$(ip route get 8.8.8.8 | sed -n 's/.*dev \([^\ ]*\).*/\1/p')
echo "IP: "$IP"\ninterface: "$IF
```
пример вывода
```
IP: 65.109.56.215
interface: enp41s0
```

- Загружаем и запускаем установочный скрипт
```bash
curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh
chmod +x wireguard-install.sh
./wireguard-install.sh
```

- Запускается мастер, который предложит ответить на несколько вопросов. Пример ответов, смотрим комментарии:

```bash
Welcome to the WireGuard installer!
...
# Сверяем со своими данными полученными ранее
IPv4 or IPv6 public address: 134.209.92.184 # IP адрес
Public interface: eth0  # Интерфейс

# Далее возможны дефолтные ответы
WireGuard interface name: wg0 # интерфейс WireGuard, default
Server WireGuard IPv4: 10.66.66.1 # VPN IPv4, default
Server WireGuard IPv6: fd42:42:42::1 # VPN IPv6, default
Server WireGuard port [1-65535]: 61690 # Порт, default
First DNS resolver to use for the clients: 1.1.1.1 # DNS1, default
Second DNS resolver to use for the clients (optional): 1.0.0.1 # DNS2, default
...
You will be able to generate a client at the end of the installation.
Press any key to continue... # Нажимаем любую клавишу
```
- Завершение установки.

После установки, будет предложено добавить клиента по умолчанию, отвечаем на несколько вопросов:
```bash
Client name: rdz2 # задаем свое имя клиента
Client WireGuard IPv4: 10.66.66.2 # default
Client WireGuard IPv6: fd42:42:42::2 #d efault
```

Будут сгенерированы ключи клиента, показан QR код для подключения и создан конфигурационный файл 
```bash
Your client config file is in /root/wg0-client-rdz2.conf
```
Как видим, у меня конфигурационный файл находится в папке `/root` под именем `wg0-client-rdz2.conf`. Этот файл нам понадобится в дальнейшем для подключения. Для добавления нового пользователя, запускаем скрипт еще раз:
```bash
cd $HOME
./wireguard-install.sh
```
Меню:
```bash
Welcome to WireGuard-install!
The git repository is available at: https://github.com/angristan/wireguard-install

It looks like WireGuard is already installed.

What do you want to do?
   1) Add a new user # Добавить пользователя
   2) List all users # Список всех пользователей
   3) Revoke existing user # Удалить существующего пользователя
   4) Uninstall WireGuard # Удалить WireGuard
   5) Exit # Выход
Select an option [1-5]: 
```
Выбираем `1` и проходим мастера еще раз.

- Подключение

Для подключения нам понадобится программа-клиент, неполный список привел ниже, официально поддерживаемые клиенты смотрим на сайте [WireGuard](https://www.wireguard.com/install/)

Windows [Tunsafe](https://tunsafe.com/download)

Android [WireGuard](https://play.google.com/store/apps/details?id=com.wireguard.android&hl=en&gl=US)

iOS [WireGuard](https://apps.apple.com/ru/app/wireguard/id1441195209)

macOS [WireGuard](https://apps.apple.com/ru/app/wireguard/id1451685025?mt=12)

При подключении клиента, достаточно лишь отсканировать QR код или загрузить config файл.
