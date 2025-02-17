Файлы с расширением `.desktop` в Linux — это текстовые файлы, которые определяют ярлыки для приложений, ссылок и команд. Они позволяют запускать приложения из меню, создавать ярлыки на рабочем столе или панелях. Формат `.desktop` используется большинством графических окружений, включая GNOME, KDE, XFCE и Cinnamon.

### Структура файла `.desktop`
Файл `.desktop` содержит несколько обязательных и дополнительных полей, оформленных в формате `ключ=значение`. Основные параметры начинаются с `[Desktop Entry]`, который определяет тип записи.

#### Пример простого `.desktop` файла:
```ini
[Desktop Entry]
Version=1.0
Type=Application
Name=Firefox
Comment=Web Browser
Exec=firefox %u
Icon=firefox
Terminal=false
Categories=Network;WebBrowser;
```

#### Разбор основных ключей
- **Version**: версия файла `.desktop`. Обычно указывается `1.0`.
- **Type**: тип записи. Основные типы:
  - `Application` — для приложений,
  - `Link` — для ссылок,
  - `Directory` — для каталогов.
- **Name**: имя, которое будет отображаться в меню или на рабочем столе.
- **GenericName**: родовое имя приложения (например, "веб-браузер" для Firefox), отображается в некоторых меню.
- **Comment**: краткое описание, отображаемое при наведении.
- **Exec**: команда для запуска приложения. Используйте:
  - `%f` — для одного файла,
  - `%F` — для списка файлов,
  - `%u` — для одного URL,
  - `%U` — для списка URL,
  - `%i` — для значка,
  - `%c` — для имени.
- **Icon**: имя значка приложения. Система ищет иконку по имени в темах значков.
- **Terminal**: принимает значения `true` или `false`. Если `true`, приложение запустится в терминале.
- **Categories**: категории, по которым приложение будет классифицироваться в меню. Примеры категорий: `Utility`, `Development`, `Education`, `Game`, `Graphics`, `Network`, `Office`, `Settings`, `System`, `AudioVideo`.

#### Дополнительные ключи
- **Path**: рабочая директория, из которой запускается приложение.
- **NoDisplay**: если `true`, приложение не будет отображаться в меню.
- **Hidden**: если `true`, значок скрыт, но не удалён.
- **OnlyShowIn**: указывает, в каких окружениях отображать (например, `GNOME`, `KDE`).
- **NotShowIn**: указывает, в каких окружениях не отображать.

### Создание файла `.desktop`
Чтобы создать новый `.desktop` файл:
1. Создайте файл в директории `~/.local/share/applications/` (для пользователя) или `/usr/share/applications/` (для всех пользователей).
   ```bash
   nano ~/.local/share/applications/myapp.desktop
   ```
2. Добавьте нужные параметры.
3. Сохраните файл и сделайте его исполняемым:
   ```bash
   chmod +x ~/.local/share/applications/myapp.desktop
   ```

После этого приложение станет доступным в меню приложений.