Чтобы установить PHP 8.3 и необходимые модули на Ubuntu, можно воспользоваться официальным репозиторием `ppa:ondrej/php`, который регулярно обновляется с актуальными версиями PHP. Вот пошаговая инструкция:

### 1. Обновите пакеты и установите зависимости
Обновите индекс пакетов и установите нужные пакеты:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y software-properties-common
```

### 2. Добавьте PPA-репозиторий от Ondřej Surý
Этот репозиторий содержит различные версии PHP, включая PHP 8.3:

```bash
sudo add-apt-repository ppa:ondrej/php
sudo apt update
```

### 3. Установите PHP 8.3
Теперь можно установить PHP 8.3:

```bash
sudo apt install -y php8.3
```

### 4. Установите необходимые модули для PHP 8.3
Вы можете установить модули, которые требуются для вашего проекта. Вот пример нескольких часто используемых модулей:

```bash
sudo apt install -y php8.3-cli php8.3-fpm php8.3-common php8.3-mysql php8.3-xml php8.3-mbstring php8.3-curl php8.3-zip php8.3-intl php8.3-gd php8.3-soap php8.3-bcmath php8.3-imagick
```

**Описание популярных модулей**:
- **php8.3-cli** — команда `php` для работы в терминале.
- **php8.3-fpm** — PHP FastCGI Process Manager (используется для работы с веб-серверами, такими как Nginx).
- **php8.3-mysql** — модуль для работы с базой данных MySQL.
- **php8.3-xml** — поддержка работы с XML.
- **php8.3-mbstring** — поддержка работы с многобайтовыми строками (нужно для UTF-8).
- **php8.3-curl** — поддержка работы с cURL для отправки HTTP-запросов.
- **php8.3-zip** — поддержка работы с архивами.
- **php8.3-intl** — поддержка интернационализации.
- **php8.3-gd** — поддержка обработки изображений.
- **php8.3-soap** — поддержка SOAP.
- **php8.3-bcmath** — библиотека для работы с большими числами.
- **php8.3-imagick** — поддержка работы с изображениями через ImageMagick.

### 5. Проверьте установленную версию PHP
Чтобы убедиться, что PHP 8.3 установлен и активен, выполните команду:

```bash
php -v
```

### 6. Настройка PHP (необязательно)
Файл конфигурации PHP обычно находится по пути `/etc/php/8.3/cli/php.ini` (для CLI) и `/etc/php/8.3/fpm/php.ini` (для FPM). Если требуется изменить параметры (например, `memory_limit` или `upload_max_filesize`), отредактируйте соответствующий файл:

```bash
sudo nano /etc/php/8.3/fpm/php.ini
```

После внесения изменений перезапустите PHP-FPM:

```bash
sudo systemctl restart php8.3-fpm
```

### 7. Добавьте PHP 8.3 в веб-сервер (Nginx или Apache)

**Для Apache**:
Убедитесь, что модуль PHP для Apache включен:

```bash
sudo a2enmod php8.3
sudo systemctl restart apache2
```

**Для Nginx**:
В конфигурации вашего сайта укажите сокет PHP-FPM, чтобы Nginx мог передавать запросы PHP:

```nginx
location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
}
```

Затем перезапустите Nginx:

```bash
sudo systemctl restart nginx
```

Теперь PHP 8.3 и необходимые модули установлены и настроены!