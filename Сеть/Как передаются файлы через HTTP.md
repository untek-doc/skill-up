Передача файлов через HTTP происходит с использованием метода **POST** или реже **PUT** в сочетании с заголовками и специальными форматами данных. Основной механизм передачи файлов — это отправка данных через **multipart/form-data**, который позволяет передавать как файлы, так и другие данные в одном запросе.

### Передача файлов с использованием метода POST

#### 1. **Заголовки HTTP-запроса**

Когда клиент отправляет файл на сервер, заголовки HTTP-запроса содержат следующую информацию:

- **Content-Type**: Этот заголовок указывает тип содержимого, который клиент отправляет. Для передачи файлов используется значение `multipart/form-data`.
  
  Пример:
  ```
  Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW
  ```

- **Content-Length**: Этот заголовок указывает размер всего тела запроса (включая данные файла) в байтах.

#### 2. **Тело запроса**

Тело HTTP-запроса состоит из нескольких частей, каждая из которых начинается с разделителя (`boundary`). В теле запроса передается информация о файле (например, его имя) и сам файл в закодированном виде.

Пример HTTP-запроса для передачи файла:
```
POST /upload HTTP/1.1
Host: www.example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Length: 138

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="example.png"
Content-Type: image/png

<binary file data>

------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

#### Описание полей:
- **boundary**: Это уникальный разделитель, который отделяет части данных (например, текст и файлы).
- **Content-Disposition**: Указывает, что это часть данных файла. Здесь передается имя файла (`filename`) и имя параметра формы (`name`).
- **Content-Type**: Указывает тип содержимого передаваемого файла, например, `image/png`.

#### 3. **Прием файла на сервере**

Сервер, получив такой запрос, обрабатывает тело запроса, извлекая файл. На серверной стороне можно использовать разные технологии для обработки загруженных файлов:

- В PHP для работы с загрузкой файлов используются суперглобальные переменные `$_FILES`.
- В Node.js можно использовать пакеты вроде `multer` для обработки файлов, отправленных через HTTP-запрос.

#### Пример обработки файла в PHP:

```php
if ($_FILES['file']['error'] === UPLOAD_ERR_OK) {
    $tmpName = $_FILES['file']['tmp_name'];
    $fileName = $_FILES['file']['name'];
    move_uploaded_file($tmpName, "uploads/$fileName");
    echo "File uploaded successfully!";
} else {
    echo "Failed to upload file.";
}
```

#### Пример обработки файла в Node.js с Multer:

```javascript
const express = require('express');
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

const app = express();

app.post('/upload', upload.single('file'), (req, res) => {
  res.send('File uploaded successfully!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

### Передача файлов с использованием PUT

Метод **PUT** также может использоваться для передачи файлов на сервер, но он обычно применяется для прямой загрузки файлов по определенному URL, когда клиент четко знает местоположение, куда нужно записать файл.

Пример PUT-запроса:
```
PUT /uploads/example.png HTTP/1.1
Host: www.example.com
Content-Type: image/png
Content-Length: 138

<binary file data>
```

В этом случае сервер ожидает содержимое файла непосредственно в теле запроса и сохраняет его по указанному пути.

### Заключение

Передача файлов через HTTP обычно осуществляется через метод **POST** с использованием `multipart/form-data`. Этот формат позволяет передавать как файлы, так и дополнительные данные формы. Сервер получает файл, обрабатывает его и сохраняет на диске.