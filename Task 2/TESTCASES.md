# API Test Cases
Данный документ содержит тест-кейсы для проверки микросервиса объявлений Avito.  
---
## 1.1 QA-intership/api/1/item/{id}
## Post https://qa-internship.avito.com/api/1/item - Сохранение объявления
Headers:
| **Content-Type** | **Accept** |
|--------------|--------|
| application/json | application/json|


Body:  
Method: row 
```
{  
  "sellerID": "<integer>",  
  "name": "<string>",  
  "price": "<integer>",  
  "statistics": {  
    "likes": "<integer>",  
    "viewCount": "<integer>",  
    "contacts": "<integer>"  
  }  
}
```
- sellerID - integer (диапозон 111111 - 999999)
- name - string (более 2990 символов)
- price - integer (макс. 19 символов)
- likes - integer (макс. 19 символов)
- viewCount - integer (макс. 19 символов)
- contacts - integer (макс. 19 символов)

**Ожидаемый результат:** 200 OK  
```
{
  "id": "<string>",
  "sellerId": "<integer>",
  "name": "<string>",
  "price": "<integer>",
  "statistics": {
    "likes": "<integer>",
    "viewCount": "<integer>",
    "contacts": "<integer>"
  },
  "createdAt": "<string>"
}
```
**Примечание:** При отправке запроса поле id принимает знасение integer, в документации при выводе указано, что поле id принимает знасение string.

**Ожидаемый результат:** 400 Bad request  
```
{
  "result": {
    "messages": {
      "culpa_b92": "<string>",
      "enim_24f": "<string>",
      "mollit_aa": "<string>"
    },
    "message": "<string>"
  },
  "status": "<string>"
}
```

---

## 1.1.1 Сохрание объявления с валидными данными
Body:  
Method: row 
```
{
  "sellerID": 462322,
  "name": "Test",
  "price": 100000,
  "statistics": {
    "likes": 200,
    "viewCount": 456,
    "contacts": 79855
  }
}
```
---
## Шаги воспроизведения:
- заполнить поля валидными данными
---

## Фактический результат:
Response:  
200 OK  
Body:  
```
{
    "status": "Сохранили объявление - bdf3e5bd-8b07-4b9e-863b-79fe3de92705"
}
```
---

## Комментарий  
В документации в Postman в разделе **Success response** показан пример объекта (пример ответа приведен в **Ожидаемом результате** этого документа). Коды состояния совпадают, однако тела ответов не совпадают, то есть несоответствие между документацией и фактическим поведением (Приоритет Low).

---
## 1.1.2 Сохрание объявления с невалидными данными
Body:  
Method: row 
```
{
  "sellerID": "0",
  "name": 123,
  "price": "5902847612039487123",
  "statistics": {
    "likes": "5902847612039487123",
    "viewCount": "5902847612039487123",
    "contacts": "5902847612039487123"
  }
}
```
---
## Шаги воспроизведения:
- заполнить поля невалидными данными
---
## Фактический результат:
Response:  
400 Bad request  
Body:  
```
{
    "result": {
        "message": "",
        "messages": {}
    },
    "status": "не передано тело объявления"
}
```
---
## Комментарий  
В документации в Postman в разделе **Bad request** показан пример объекта (пример ответа приведен в **Ожидаемом результате** этого документа). Коды состояния совпадают, однако тела ответов не совпадают, то есть несоответствие между документацией и фактическим поведением (Приоритет Low).

---

## 1.1.3 Уникальность идентификатора объявления
Body:  
Method: row 
```
{
  "sellerID": 462311,
  "name": "Test",
  "price": 5902847612039487123,
  "statistics": {
    "likes": 5902847612039487123,
    "viewCount": 5902847612039487123,
    "contacts": 5902847612039487123
  }
}
```
---
## Шаги воспроизведения:
- заполнить поля валидными данными
- создать 2 объявления с одинаковыми входными данными
---
## Фактический результат:
Response_1:  
200 OK  
Body:  
```
{
    "status": "Сохранили объявление - 87a021c7-2618-4e10-9ab1-fe7c6b7b614b"
}
```
---
Response_2:  
200 OK  
Body:  
```
{
    "status": "Сохранили объявление - d7d270c4-54f3-4678-a16c-3ad788697208"
}
```
---
## Комментарий  
Оба объявления создались успешно, имеют одинаковые данные.

---

## 1.1.4 Сохранение объявление при некорректном sellerID
Body:  
Method: row 
```
{
  "sellerID": 123,
  "name": "123",
  "price": 5902847612039487123,
  "statistics": {
    "likes": 5902847612039487123,
    "viewCount": 5902847612039487123,
    "contacts": 5902847612039487123
  }
}
```
---
## Шаги воспроизведения:
- заполнить поля валидными данными
- заполнить поле sellerID не в соответственности диапозону (111111 - 999999)
---
## Фактический результат:
Response:  
200 OK  
Body:  
```
{
    "status": "Сохранили объявление - 40dda51f-9d58-4020-9442-99929b6f93cd"
}
```
---
## Комментарий
Сервис не выполняет валидацию sellerID. Некорректное значение не блокирует создание объявления. Баг заведен в файле [BUGS.md](/Task%202/BUGS.md).

---

## 1.1.5 Создание объявления с null (пустыми) полями
Body:  
Method: row 
```
{
  "sellerID": null,
  "name": null,
  "price": null,
  "statistics": {
    "likes": null,
    "viewCount": null,
    "contacts": null
  }
}
```
---
## Шаги воспроизведения:
- поочередно заполнять поля null, чтобы только у одного из полей был null, а у остальных полей были валидные данные
---
## Фактический результат:
Response_1:  
400 Bad request  
Body_1:  
```
{
    "result": {
        "message": "поле sellerID обязательно",
        "messages": {}
    },
    "status": "400"
}
```
---
Response_2:  
400 Bad request  
Body_2:  
```
{
    "result": {
        "message": "поле name обязательно",
        "messages": {}
    },
    "status": "400"
}
```
---
Response_3:  
400 Bad request  
Body_3:  
```
{
    "result": {
        "message": "поле price обязательно",
        "messages": {}
    },
    "status": "400"
}
```
---
Response_4:  
400 Bad request  
Body_4:  
```
{
    "result": {
        "message": "поле likes обязательно",
        "messages": {}
    },
    "status": "400"
}
```
---
Response_5:  
400 Bad request  
Body_5:  
```
{
    "result": {
        "message": "поле viewCount обязательно",
        "messages": {}
    },
    "status": "400"
}
```
---
Response_6:  
400 Bad request  
Body_6:  
```
{
    "result": {
        "message": "поле contacts обязательно",
        "messages": {}
    },
    "status": "400"
}
```
---
## Комментарий
Микросервис проверяет каждое поле на валидацию. В документации в Postman в разделе **Bad request** показан пример объекта (пример ответа приведен в **Ожидаемом результате** этого документа). Коды состояния совпадают, однако тела ответов не совпадают, то есть несоответствие между документацией и фактическим поведением (Приоритет Low).

---

## 1.2 QA-intership/api/1/item/{id}
## Get https://qa-internship.avito.com/api/1/item/:id - Получить объявление по идентификатору
Headers:
| **Accept** |
|--------|
| application/json|

**Ожидаемый результат:** 200 OK  
```
[
  {
    "id": "<string>",
    "sellerId": "<integer>",
    "name": "<string>",
    "price": "<integer>",
    "statistics": {
      "likes": "<integer>",
      "viewCount": "<integer>",
      "contacts": "<integer>"
    },
    "createdAt": "<string>"
  },
  {
    "id": "<string>",
    "sellerId": "<integer>",
    "name": "<string>",
    "price": "<integer>",
    "statistics": {
      "likes": "<integer>",
      "viewCount": "<integer>",
      "contacts": "<integer>"
    },
    "createdAt": "<string>"
  }
]
```

**Ожидаемый результат:** 400 Bad Request  
```
{
  "result": {
    "messages": {
      "culpa_b92": "<string>",
      "enim_24f": "<string>",
      "mollit_aa": "<string>"
    },
    "message": "<string>"
  },
  "status": "<string>"
}
```
**Ожидаемый результат:** 404 Not Found
```
{
  "result": "laborum",
  "status": "cillum enim eiusmod"
}
```
**Ожидаемый результат:** 500 Internal Server Error
```
{
  "result": {
    "messages": {},
    "message": "<string>"
  },
  "status": "<string>"
}
```

---
