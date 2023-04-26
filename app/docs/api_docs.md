# Документация к API PyMessages

Здесь расположена вся документация к API

## UsersResource

Ресурс для работы с пользователями.
Реализует GET, PUT, POST запросы.
Точка входа:
/api/users/\<user_id\>

### GET

Возвращает краткую информацию о пользователе по его user_id в формате json:
```
{
  "user": 
    {"avatar": "avatar.jpg",
    "first_name": "test_first_edited", 
    "second_name": "test_second", 
    "user_id": "7ts2hzzjgkugzv"}
}
```
Если запрашивается инфомрация о текущем пользователе, или user_id не передан, то
результатом запроса будет полная информация о текущем пользователе (если он 
авторизован) в формате json:
```
{
  "user": 
    {"additional_inf": null,
    "age": null, 
    "avatar": "avatar.jpg", 
    "city": null, 
    "created_date": "2020-06-05 14:07", 
    "email": "test@gmail.com", 
    "first_name": "test_first_edited", 
    "is_confirmed": false,
    "phone_number": null, 
    "second_name": "test_second", 
    "user_id": "7ts2hzzjgkugzv"}
}
```

*Пользователь считается авторизованным, если вместе с запросом передан 
access_token (он может передаваться либо в теле запроса в поле access_token, 
либо в строке запроса в параметре jwt, либо в cookie в поле 
access_token_cookie)*

### PUT
Данный запрос позволяет редактирвоать информацию пользователя по user_id.
Данные должны передавать в теле соответствующего PUT-запроса. В результате 
успешного запроса возвращается json, содержащий user_id пользователя:
```
{"user_id": "7ts2hzzjgkugzv"}
```
При смене email или пароля user_id изменяется, а потому и возвращается в 
результате запроса. 

### POST
Запрос отвечающий за добавление нового пользователя. Обязательные данные для
создания нового пользваотеля: first_name, second_name, password, email. Все 
они также должны передаваться в теле POST-запроса. В случае удачного запроса 
возвращается следующий json:
```
{"success": "OK"}
```

## UsersListResource

Ресурс для работы со списком пользователей.
Реализует GET запрос.
Точка входа:
/api/v1/users

### GET

В теле запроса передаются параметры first_name и/или second_name, либо параметр 
search_request. Вместе с этим передаются параметры опциональные параметры 
start (по умолчанию 0) и limit (по умолчанию 20). В результате возвращается 
список пользователей, у которых first_name и/или second_name совпадают с 
переданными в запросе, либо список пользователей, у которых first_name, 
second_name или user_id частично или полностью совпадает с search_request 
(проверка происходит без учёта регистра). Количество полученных пользователей 
ограничено параметром limit (но не более 20 пользователей). Параметр start 
определяет, начиная с какого пользователя будет начинаться список. Таким 
образом, для получения, к примеру, 40 пользователей, необходимо сначала 
выполнить один запрос, где start=0&limit=20, а затем второй, где 
start=20&limit=20 (остальные параметры в запросе должны быть идентичны).
Если не передан ни один из параметров first_name, second_name или 
search_request, то будут получены все пользователи (не забывайте про лимит).
Пример полученного json:
```
{
  "users": 
    [
      {"avatar": "avatar.jpg", "first_name": "Алена", "second_name": "Лебедева", 
      "user_id": "kxzidldgi4svn4bi1"}, 
      {"avatar": "avatar.jpg", "first_name": "Давид", "second_name": "Кудрявцев", 
       "user_id": "p0j4lsuruen"}, 
      {"avatar": "avatar.jpg", "first_name": "Юрий", "second_name": "Власов", 
       "user_id": "q5ysmkfyjzqeekvfnir2"}, 
      {"avatar": "avatar.jpg", "first_name": "Давид", "second_name": "Федотов", 
       "user_id": "3nvgw0l8gp7z9b"}, 
      {"avatar": "avatar.jpg", "first_name": "Людмила", 
       "second_name": "Виноградова", "user_id": "xc4trzgxsr"}
  ]
}
```

## LoginResource

Ресурс входа, позволяющий получить все необходимые JWT токены. Реализует 
POST-запрос. Точка входа: /api/v1/login

### POST

В теле запроса передаются параметры email, password, а также опциональный 
параметр expires_in. Параметры email и password - это эл. почта и пароль от 
аккаунта, для которого необходимо получить токен. expires_in - время жизни 
access токена в секундах (значение по умолчанию задаётся в поле
JWT_ACCESS_TOKEN_EXPIRES в конфигурации приложения). Возвращает json с access
и refresh токенами:
```
{"access_token":  "token",
"refresh_token":  "another_token"}
```

## RefreshResource

Ресурс входа, позволяющий обнвить refresh токен. К этому ресурсу 
могут обращаться только пользователи, имеющие при себе refresh_token 
(он может передаваться либо в теле запроса в поле refresh_token, 
либо в строке запроса в параметре jwt, либо в cookie в поле 
refresh_token_cookie). Реализует POST-запрос. Точка входа: /api/v1/refresh

### POST

Возвращает следующий json:
```
{"access_token":  "token"}
```

## MessagesResource

К этому API могут обращаться только авторизированные пользователи.
Ресурс для работы с сообщенями.
Реализует POST-запрос.
Точка входа:
/api/v1/messages

### POST
Передаются все данные, необходимые для создания сообщения: sender_id,
chat_id или receiver_id, text. В случае успешной отправки сообщения 
возвращается json типа:
```
{"success": "OK"}
```


## MessagesListResource

К этому API могут обращаться только авторизированные пользователи.
Ресурс для работы с сообщенями.
Реализует GET-запрос.
Точка входа:
/api/v1/messages

### GET
При данном запросе либо не передается ничего, тогда результатом будет список
последних сообщений по 1 от каждого друга текущего пользователя. Если
передается receiver_id, то результатом будет список сообщений между текущим 
пользователем и пользователем с переданным receiver_id. 
Если передается receiver_id и date, то результатом будет список сообщений между 
текущим пользователем и пользователем с переданным receiver_id, которые были 
отправлены после переданной даты date. Вместе receiver_id может передаваться 
chat_id, тогда будет получен список сообщений из чата с переданным id.
Например, ответ может быть таким:
```
{
  "messages": 
    [
      {"chat_id": 1,
      "is_read": false, 
      "sender_id": "di8nwfeigna_e7jwiicj", 
      "sending_date": 1591366268.613312,
      "text": "test"},
      {"chat_id": 1,
      "is_read": false, 
      "sender_id": "di8nwfeigna_e7jwiicj", 
      "sending_date": 1591366290.124467,
      "text": "Hello World!"}
    ]
}
```

## UsersFriendsResource

К этому API могут обращаться только авторизированные пользователи.
Ресурс для работы с друзьями пользователей.
Реализует GET, POST, DELETE запросы.
Точка входа:
/api/v1/users_friends/

### POST
При данном запросе передаются обязательные аргументы user_id и action. Если 
action=add, то пользователю с ID, равным user_id, отправляется заявка в друзья. 
Если action=accept, то подтверждается заявка от пользователя с ID, равным 
user_id (т.е. пользователи становятся друзьями). Если же action=deny, то 
заявка от пользователя с ID, равным user_id, отклоняется. В случае успеха 
ответ будет содержать следующий json:
```
{"success": "OK"}
```

### DELETE
Этот запрос принимает обязательный аргумент user_id. Запрос в друзья к этому 
пользователю будет удален. В случае успеха ответ будет содержать следующий json:
```
{"success": "OK"}
```

## UsersFriendsListResource

К этому API могут обращаться только авторизированные пользователи.
Ресурс для работы со списком друзей пользователя. Реализует GET-запрос.
Точка входа:
/api/v1/users_friends

### GET
Если ничего не переадется, то результатом будет список друзей текущего
залогиненного пользователя. Вместе с запросом может быть передан параметр type, 
тогда, если type=incoming, то будут возвращены все исходящие заявки в друзья. 
Иначе, если type=outgoing, то будут возвращены все входящие заявки в друзья.
Например, ответ может быть таким:
```
{
  "friends": 
    [
      {"avatar": "avatar.jpg", "first_name": "friend_second", 
       "is_accepted": null, "second_name": "test", 
       "user_id": "om3rshuqr5ptsr5s3o"}
    ]
}
```

## ChatsResource

К этому API могут обращаться только авторизированные пользователи.
Русурс для работы с чатами.
Реализует GET, POST, DELETE запросы.
Точка входа:
/api/v1/chats/

### GET
Возвращает чат с id, переданным в параметре chat_id, либо чат с пользователем,
id которого равен переданному параметру chat_with.
```
{
  "chat": 
    {"first_author_id": 109, "id": 3, "second_author_id": 110, "title": null}
}
```

### POST
При данном запросе создается чат по переданным аргументам
(first_author_id, second_author_id - обязательные, title - необязательный).
В случае успешного выполнения будет получен следующий json:
```
{"success": "OK"}
```

### DELETE
При данном запросе переадется id чата (параметр chat_id) либо user_id 
пользователя, с которым ведётся чат (параметр chat_with). Этот чат и все 
сообщения из него будут удалены. В случае успешного выполнения будет получен 
следующий json:
```
{"success": "OK"}
```

## ChatsListResource

К этому API могут обращаться только авторизированные пользователи.
Русурс для работы с чатами.
Реализует GET-запрос.
Точка входа:
/api/v1/chats

### GET
Возвращает список чатов текущего пользователя в виде json:
```
{
  "chats": 
    [
      {"first_author_id": 109, "id": 3, "second_author_id": 110, "title": null}
    ]
}
```

## TokensResource

Ресурс для работы с токенами подтверждения аккаунта пользователя. Реализует 
GET и POST запросы. Точка входа: /api/v1/tokens/

### GET

В теле запроса передаются параметры email и password. Если данные верны, 
возвращается json, содержащий токен для подтверждения аккаунта пользователя:
```
{"token": "some_token"}
```

### POST

В теле передаётся единственный параметр token. Если токен валидный, аккаунт 
пользователя подтверждается и возвращается json:
```
{"success": "OK"}
```