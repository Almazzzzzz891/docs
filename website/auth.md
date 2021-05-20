# Авторизация

## Навигация
- [Авторизация пользователя](#авторизация-пользователя)

## Авторизация пользователя
### **Запрос**
```
POST /login
{
    "email": <String>,
    "password": <String>
}
```

### **Ответ**
```json
{
    "message": <String>,
    "token": <String>
}
```

После этого полученный токен необходимо передавать или в cookie, или в заголовке `Authorization`.

Для передачи в cookie, токен необходимо указывать в формате `token=<token>`.

Пример заголовка с передачей токена в сookie:
```http
Cookie: token=Bearer%20eyJhbCgiOiJIYzI1NiI..
```

Пример заголовка с передачей токена в `Authorization`:
```http
Authorization: Bearer eyJhbCgiOiJIYzI1NiI..
```


